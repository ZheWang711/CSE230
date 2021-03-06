# Making Own Types and Typeclasses

## Algebraic data types intro
* `data` meas we are defining a new data type
    * `data Bool = False | True`
    * Part before `=` denotes the type
    * Parts after `=` are **value constructors**
* When we write a value constructor, we can optionally add some types after it and those types define the value it will contain.
    * e.g. `data Shape = Circle Float Float Float | Rectangle Float Float Float Float`
* Value constructors are actually functions that ultimately return a value of a data type.
    * e.g `Circle :: Float -> Float -> Float -> Shape`
* Make a function takes a shape are returns its surface
    ```Haskell
    surface :: Shape -> Float
    surface (Circle _ _ r) = pi * r ^ 2
    surface (Rectangle x1 x2 y1 y2) = (abs $ x2 - x1) * (abs $ y2 - y1)
    ```
    * we couldn't write a type declaration of `Circle -> Float` because `Circle` is not a type, `Shape` is.
    * **We can pattern matched against constructors -- just write a constructor and then bind its fields to names**
    * When we try to print a value out in the prompt, Haskell first runs `show` function to get the string representation of our value and then it prints that out to the terminal.
        * to make `Shape` type part of the `Show` typeclass  
        ```Haskell
        data Shape = Circle Float Float Float | Rectangle Float Float Float Float deriving (Show)
        ```
* Value constructors are functions, we can partially apply them and everything. `map (Circle 10 20) [4,5,6,6]`

* Intermediate data type
    * Common to use same name of constructor as type if there is only one constructor
    ```Haskell
    data Point = Point Float Float deriving (Show)  
    data Shape = Circle Point Float | Rectangle Point Point deriving (Show)
    
    surface :: Shape -> Float  
    surface (Circle _ r) = pi * r ^ 2  
    surface (Rectangle (Point x1 y1) (Point x2 y2)) = (abs $ x2 - x1) * (abs $ y2 - y1 
    
    nudge :: Shape -> Float -> Float -> Shape  
    nudge (Circle (Point x y) r) a b = Circle (Point (x+a) (y+b)) r  
    nudge (Rectangle (Point x1 y1) (Point x2 y2)) a b = Rectangle (Point (x1+a) (y1+b)) (Point (x2+a) (y2+b))  
    
    baseCircle :: Float -> Shape  
    baseCircle r = Circle (Point 0 0) r  
  
    baseRect :: Float -> Float -> Shape  
    baseRect width height = Rectangle (Point 0 0) (Point width height)
    
    -- if you don't want to deal with intermediate type, Point, just construct a baseCircle / baseRect and nudge it!
    
    ```
* Export data types in your modules -- write your type along with the functions you are exporting and then add some parentheses and in them specify the value construcots that you want to export for it, separated by commas.
    * Export all the value constructors for a given type -- write `..`
```Haskell
module Shapes   
( Point(..)  
, Shape(..)  
, surface  
, nudge  
, baseCircle  
, baseRect  
) where  
```

* Could also export any value constructors for `Shape` by just writing `Shape` in the export statement. -- can only make shapes by auxiliary functions.
    * Makes a data types more abstract, hide their implementation.

## Record synatx

* Haskell can automatically make field query function
* Displays differently
* Use record syntax when a constructor has several fields and it's not obvious which field is which
```Haskell
data Person = Person { firstName :: String
                       , lastName :: String
                       , age :: Int
                       , height :: Float
                       ,phoneNumber :: String
                       , flavor :: String
                       } deriving (Show)
```
* Different Pattern Matching for constructor

```Haskell
-- record syntax
data Car = Car { company :: String
                , model :: String
                , year :: Int
                } deriving(Show)


tellCar :: Car -> String
tellCar (Car {company = c, model = m, year = y}) = "This " ++ c ++ " " ++ m ++ " was made in " ++ show y

tellCar' :: Car -> String
tellCar' (Car c m y) = "This " ++ c ++ " " ++ m ++ " was made in " ++ show y
```

## Type Parameters
* **Value constructor** can take some values parameters and then produce a new value

* **Type Constructor** take type as parameters to produce new types
```Haskell
data Maybe a = Nothing | Just a
```
    * `a` is the type parameter
    * `Maybe` is a type constructor
* Usually we use them when our data type would work regardless of the type of the value it holds.

**A bad example of using type parameters**
```Haskell
data Car a b c = Car { company :: a
                     , model :: b
                     , year :: c
                     } deriving(Show)


-- the type signature is much more complicated
-- the only benefit is is that we can use any type that's an instance of the show type class as the type for c
tellCar :: (Show a) => Car String String a -> String
tellCar (Car {company = c, model = m, year = y}) = "This " ++ c ++ " " ++ m ++ " was made in " ++ show y
```

**A good example of using type parameters**
```haskell
-- Map k v from Data.Map
-- k is the type of keys in a map
-- v is the type of values
-- Having maps parameterized enables us to have mapping from any type to any type (As long as the type of the key is part of Ord typeclass)
```
* **Never add typeclass constraints in data declarations** -- put the typeclass constraints in the type signature of functions that **assume the constraints**

An example of parameterized vector
```Haskell
-- No typeclass constraints here!
data Vector a = Vector a a a deriving (Show)

vplus :: (Num t) => Vector t -> Vector t -> Vector t
(Vector i j k) `vplus` (Vector l m n) = Vector (i + l) (j + m) (k + n)

...

```

* Distinguish type constructor and** value constrcutor
    * When declearing a data type, **the part before `=` is type constructor**, **the parts (possibly separated by `|`) after `=` are value constructors**.
    * We have to put types in type declaration
        * `Vector t -> Vector t -> t` is correct!
        * `Vector t t t -> Vector t t t -> t` is wrong!

## Derived Instances

* "A typeclass is a sort of an interface that defines some *behavior*." A type can be made an **instance** of a typeclass if it supports that *behavior*.
    * If a type is a part of typeclass `A`, we can use the interface functions of `A` with values of that type.
* Typeclasses are more like interfaces, we don't make data from typeclasses.
    * Instead, we first make our data type and then we think about what it can act like. (e.g. If a type act like something that can be ordered , we make it an instance of `Ord` typeclass)

* How can we manually make our types instances of typeclasses? By implement the functions defined by typeclasses!
    * Haskell can automatically make our type an instance of following typeclasses: `Eq`, `Ord`, `Enum`, `Bounded`, `Show`, `Read` (by `deriving` keyword).

Example: `Eq`, `Show`, and `read`
```Haskell
data Person = Person { firstName :: String
                     , lastName :: String
                     , age :: Int
                     } deriving (Eq, Show, Read)
```
* Generate a data, even in different field order!
    * `mikeD = Person {lastName = "Diamond", age = 43, firstName = "Michael"}`
* Read function, need **type annotation**
    * `s = read "Person {firstName = \"Michael\", lastName = \"Diamond\", age = 43}" :: Person`
* Equality test
    * `eq = s == mikeD`
* Can also read parameterized types, but have to fill in the type parameters.
    * `read "Just 't'" :: Maybe a`
* `Ord` type class -- types that have values can be ordered
    * The value which was made with a constructor that's defined first is considered smaller.
        * `Nothing` is always smaller than a value of `Just something`.
        * If we compare 2 `Just` values, the nit goes to compare what's inside them.
* Can easily use algebraic data types to make enumerations -- `Enum` and `Bounded` typeclasses help us!
    * `Enum` -- for things that have predecessors (`pred`) and successors (`succ`)
        * All constructors must be nullary (take no parameters)
    * `Bounded` -- for things that have a lowest possible value and highest possible value (`minBound`, `maxBound`)

Example -- `Enum` and `Bounded`
```Haskell
data Day = Monday | Tuesday | Wednesday | Thursday | Friday | Saturday | Sunday
    deriving (Eq, Ord, Show, Read, Bounded, Enum)

minBound :: Day  -- Monday
maxBound :: Day  -- Sunday
succ Monday -- Tuesday
pred Saturday -- Friday
[Thursday .. Sunday] -- [Thursday, Friday, Saturday, Sunday]
[Monday, Wednesday  .. Sunday] -- [Monday,Wednesday,Friday,Sunday]
```

## Type synonyms -- only for readability

* `type`keyword  just make a synonym for an already existing type. `type String = [Char]`
* Giving `String` type synonyms is something that Haskell programmers do when they want to convey more information about what strings in their functions should be used as and what they represent.

## Typeclasses 102 -- make types instances of typeclasses by hand

* Recap:
    * Typeclasses are like interfaces. A typeclass defines some behavior, and types that can behave in that way are made instances of that typeclass
    * Behavior of a typeclass is achieved by defining functions or just type declarations that we implement.
    * A type is an instance of a typeclass ==> we can use the funtions that the typeclasses defines with that type!
    * E.g. `Eq` typeclass is for stuff that can be equated. It defines functions `==` and `/=`. If we have a type `Car` and comparing 2 cars with the equality function `==` makes ense, then it makes sense for `Car` to be an instance of `Eq`
* Define `Eq` typeclass
```Haskell
class Eq a where
    (==) :: a -> a -> Bool
    (/=) :: a -> a -> Bool
    x == y = not (x /= y)
    x /= y = not (x == y)
```

    * Type variable `a` will play the role of the type that will soon be making instance of `Eq`.
    * Not mandatory to implement the function bodies themselves, just have to specify the type declearations.
* Make instance by hand
    * use `instance` keyword.
        * `class` is defining new typeclasses
        * `instance` is for making out types instances of typeclasses
    * Minimal complete definition for the typeclasses -- minimum of functions that we have to implement
    ```Haskell
    data TrafficLight = Red | Yellow | Green
    
    -- here a is replaced with TrafficLight
    instance Eq TrafficLight where
        Red == Red = True
        Green == Green = True
        Yellow == Yellow = True
        _ == _ = False
    
    instance Show TrafficLight where
        show Red = "Red Light"
        show Yellow = "Yellow Light"
        show Green = "Green Light"
    ```
* Make typeclasses that are subclasses of other typeclasses
```Haskell
class (Eq a) => Num a where
    ...
```
Here out type `a` must be an instance of `Eq`

* Instance non-concrete type -- make it concrete!
```Haskell
instance (Eq m) => Eq (Maybe m) where
    Just x == Just y = x == y
    Nothing == Nothing = True
    _ == _ = False
```
    * class constraints in `class` declearations are used for making a typeclass a **subclass** of another typeclass
    * class constraints in `instance` declarations are used to express requirements about the contents of some type
    * If you see that a type is used as a concrete type in type declarations (like `a -> a -> Bool`), have to supply type parameters ana add parentheses to make it concrete.
    * Type you're trying to make an instance of will replace the parameter in `class` declaration.
* Want to see what instances of a typeclass are, do `:info YourTypeClass` in GHCI.

### Example: A yes/no typeclass
```Haskell
-- define a yes/no typeclass

-- class declaration
class YesNo a where
    yesno :: a -> Bool

-- define some instances
instance YesNo Int where -- for numbers
    yesno 0 = False
    yesno _ = True

instance YesNo [a] where -- for Lists
    yesno [] = False
    yesno _ = True

instance YesNo Bool where -- for bools
    yesno = id
    -- id: standard library function that takes a parameter and returns the same thing

instance YesNo (Maybe a) where -- for Maybe
-- no ned a class constraint, because we made no assumption about the contents of Maybe
    yesno (Just _) = True
    yesno Nothing = False

instance YesNo (Tree a) where -- for Tree
    yesno EmptyTree = False
    yesno _ = True

instance YesNo TrafficLight where
    yesno Red = False
    yesno _ = True
```
Now, we can define a function that take advantages of our `YesNo` typeclass
```Haskell
yesnoIf :: (YesNo y) => y -> a -> a -> a
yesnoIf yesnoVal yesResult noResult = if yesno yesnoVal then yesResult else noResult
```

### `Functor` typeclass -- things that can be mapped over

* Mapping lists is popular in Haskell. Yes, list is a part of `Functor` typeclass.

* Note from [stack overflow](http://stackoverflow.com/questions/2030863/in-functional-programming-what-is-a-functor):**A functor is a kind of collection for which if you are given a function on elements, `fmap` wiil give you back a function on collections**
    * A type `T` belongs to `Functor` type class if it has certain collection-like behavior:
        * `T` is paramterized over another type, which you should think of as the element type of the collection, the full type is something like `T Int`; `T String`...
        * If you have a function of type `a -> b` (on elements), then you have to be able to take that function and product a related function on collections, with the operator `fmap`, which is shared by every type in the `Functor` type class.
        * Example: say you have a function `even :: Int -> Bool`, then `fmap even` is an overloaded function allows you to do wonderful things:
            * Convert a list of integers to a list of booleans
            * Convert a tree of integers to a tree of booleans
            * Convert `Nothing` to `Nothing` and `Just 7` to `Just False`
        * This property is expressed by giving the type of `fmap`: `fmap :: (Functor t) => (a -> b) -> t a -> t b`

### Kind

* Kind is somewhat type of type
* Type constructors can be partially applied, just like a function can.
* `:k Int` will generate `Int :: *` which means a **concrete type**
    * Concrete doesn't take any parameters
    * Values can only have types that are concrete.
* `:k Maybe` generate `Maybe :: * -> *`, `Maybe` type constructor takes one concrete type (`Int`) then returns a concrete type (`Maybe Int`)
































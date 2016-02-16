# Making Own Types and Typeclasses

## Algebraic data types intro
* `data` meas we are defining a new data type
    * ``data Bool = False | True
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

# Functors, Applicative Functors and Monads

* What is monad ([from stack overflow](http://stackoverflow.com/questions/44965/what-is-a-monad)) ?
    * Monad is a **computation builder** that **chains operations in some specific, useful way**.
        * e.g. in list comprehension, the operation are chained such that if an operation returns a list, then the following operations are performed o every items in the list. 
        ```Haskell
        do
            x <- [1..10]
            if odd x 
                then [x * 2] 
                else []
        -- same as [x*2 | x<-[1..10], odd x]
        -- same as [1..10] >>= (\x -> if odd x then [x*2] else [])
        ```
        * The IO monad performs the operations sequentially, but passes a "hidden variable" along, which represents "the state of the world", which allows us to write I/O code in a pure functional manner.
        * `Error` monad: operations are chained such that they are performed sequentially, except if an error is thrown, in which case the rest of the chain is abandoned.
    * A monad is basically just a type that support the `>>=` operator
        * `>>=` takes a value (on the left side) and combines it with a function (on the right side), to produce a new value. This new value is then taken by the next `>>=` operator and again combined with a function to produce a new value. `>>=` can be viewed as a mini-evaluator.
        * `>>=` is overloaded for different types, so every monad has its own implementation of `>>=`
    * A monad manages the control flow: the operations are performed sequentially until a match fails, in which case the monad backtracks to the latest `<|>` and tries the next option.
        ```Haskell
        parseExpr = parseString <|> parseNumber
        
        parseString = do
            char '"'
            x <- many (noneOf "\"")
            char '"'
            return (StringValue x)
            
        parseNumber = do
            num <- many1 digit
            return (NumberValue (read num))
        ```
        
        
 


* In Haskell, we don't have to think about types belonging to a big hierarchy of types, we think about what the type can **act** like and then **connect them with appropriate type classes (that define its behaviors) **.
* We can define  typeclasses that define behavior that's very general and abstract.
* `<-` and `IO`:
    * If some value has a type of `IO String`, it's an I/O action that when performed, will go out into the real world and get some string for us, which will yields as a result.
    * Can use `<-` in `do` syntax to bind that result to a name.
    * We can see `IO` acts like a `Functor`
    * When using `fmap` a function over an I/O action, we get back an I/O action that does the same thing, but has our function applied over its result value.
    ```Haskell
    instance Functor IO where
    fmap f action = do
        result <- action
        return (f action)
    ```


* Advanced Functor usage
    * **Composition** Functions that has type `(->) r` can be instances of Functor
        * If you want to apply multiple transformations to some data inside a functor, you can declare your own function at the top level, make a lambda function or ideally, use function composition
        * `fmap` has the type `a -> b -> f a -> f b` ==> `(a -> b) -> ((->) r a) -> ((->) r b)`  
          ==> `(a -> b) -> (r -> a) -> (r -> b)`
        * The definition is **function composition**:
        ```Haskell
        instance Functor ((->) r) where
            fmap f g = (\x -> f(g x))
        ```
        * Example: `fmap (*3) (+100) 1 = 303`
    * **Lifting** a function: `fmap :: (a->b) -> (f a -> f b)` -- partially apply fmap using a function
        * Think of a Functor as a function that takes a function and returns a new function that's just like the old one, only it takes a Functor as a parameters and returns a Functor as the result.
        * The function will work on any Functor
    * `IO` action is an instance of functor
        * The result of mapping something over an I/O action will be an I/O action
        * The result value is the return value of applying the function over the original value.
        * If you want to bind the result of an I/O action to a name and apply a function to that and call that something else, do use `fmap`
    * We can think of functors as things that output values in a context.
        * Mapping over functiors can be thought of **attaching a transformation to the output of the functor that changes the value** E.g.: `fmap (+3) (*3)` -- we attach the transformation `(+3)` to the eventual output of `(*3)`. 



* **2 Functor laws**
    * **1:** If we map the `id` function over a functor, the functor that we get back should be the same as the original functor. (`id` is the identity function, where `id x = x`)
    * **2:** `fmap (f . g) = fmap f . fmap g`: compositing 2 functions and then mapping the resulting function over a functor should be the same as first mapping one function over the function and then mapping the other one. Another way to write it `fmap (f.g) F = fmap f (fmap g F)`
    * If a type obeys the functor laws, we know that calling fmap on a value of that type will only map the function over it, nothing more.

## Applicative functor

* `fmap`only provide mapping **normal function** over a functor.
    * How to deal with **function that inside a functor**? Pattern matching
        * e.g. mapping the function in `a = Just (3 *)` to `b = Just 5`: `fmap (x where Just x = a) b`
    * more general and abstract way?

* **`Applicative`** type class
```Haskell
class (Functor f) => Applicative f where
    pure :: a -> f a
    (<*>) :: f (a -> b) -> f a -> f b
```
    * `Application` is also in `Functor`, we can use `fmap` on it.
    * `pure :: a -> f a`: Putting a value in a minimal context that still holds it as its result.
    * `(<*>):: (a -> b) -> f a -> f b`: It's sort of a beefed up `fmap`!
        * `fmap` takes a function and a functor and applies the function inside the functor.
        * `<*>` takes a functor that has a function in it and another functor and sort of extracts that function from the first functor and then maps it over the second one.
        * `<*>` is left-associative
        * **Applicative style:** `pure f <*> x <*> y <*> ...`
        * `pure f <*> x` equals `fmap f x`
    * `<$>` function: just `fmap` as an infix operator
    ```Haskell
    (<$>):: (Functor f) => (a -> b) -> f a -> f b
    f <$> x = fmap f x
    -- type variables are independent of parameter names or other value names
    -- f in the signature is a constraint saying that any type constructor that replaces f should be in Functor type class
    -- f in the function body denotes a function that we map over x
    ```
    If we want to apply a function `f` between 3 applicative functors, we can write `f <$> x <*> y <*> z`
* Some Example of Applicative
    * List : `fs <*> xs = [f x | f <- fs, x <- xs]`
    ``` Haskell
    [(*0),(+100),(^2)] <*> [1,2,3] 
    = [0,0,0,101,102,103,1,4,9] `
    
    (++) <$> ["ha","heh","hmm"] <*> ["?","!","."] 
    = ["ha?","ha!","ha.","heh?","heh!","heh.","hmm?","hmm!","hmm."]
    ```
    * IO:
    ```Haskell
    instance Applicative IO where
        pure = return
        a <*> b = do
            f <- a -- first performs the first one to get the function 
            x <- b -- then performs the second one to get the value
            return (f x) -- yield that function applied to the value as its result
    ```
        * If you want binding some I/O actions to names and then calling some function on them and presenting that as the result by using `return` ==> using the applicative style.
        ```Haskell
        myAction :: IO String
        myAction = do
            a <- getLine
            b <- getLine
            return  (a ++ b)
        
        -- myAction is same as the following expression
        (++) <$> getLine <*> getLine 
        
        ```
    * Function of type `(->) r`
    ```Haskell
    instance Applicative ((->) r) where
        pure x = (\_ -> x)
        f <*> g = \x -> f x (g x)
    ```
    Example: `(\x y z -> [x,y,z]) <$> (+3) <*> (*2) <*> (/2) $ 5` = `[8.0,10.0,2.5]`

## `newtype` keyword

* [Difference](http://stackoverflow.com/questions/5889696/difference-between-data-and-newtype-in-haskell/5889784) between `data` and `newtype`
    * The constructor of `newtype` is guaranteed to be erased at compile time
    * A `newtype` only works when wrapping a data type with **a single construcor with only one field**
    *  `newtype` Just take one type and wrap it in something to present it as another type
    *  `newtype` is faster (low overhead)

* `newtype` can only turn an existing type into a new type
Example: how to map over the first element of a tuple (functor kind must be `* -> *`, but tuple has `* -> * -> *`)

```Haskell
-- plan: make the tuple into a new type, then pattern match to get its first component and apply function for it

newtype Pair b a = Pair {getPair :: (a, b)}

instance Functor (Pair c) where
    fmap f (Pair (x, y)) = Pair (f x, y)

```
    





















    





















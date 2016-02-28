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
        * 
   
   






















    





















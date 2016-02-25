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
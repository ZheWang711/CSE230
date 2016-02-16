# Feb16

```Haskell
instance Applicative Exc where
    pure = Result
    (Exn m) <*> _ = Exn m
    _ <*> Exn n = Exn n
    (Result f) <*> (Result v) = Result (f v)


instance Monad Exc where
  -- return :: a -> Exc a
  return = Result
  -- (>>=) :: Exc a -> (a -> Exc b) -> Exc b
  (Exp msg) >>= f = Exn msg
  (Resulr x) >>= f = f x
```

* How to catch a expression? 
```Haskell
lameEval :: Expr -> Int
lameEval e = case evalE r of
                    Resulty n -> n
                    Exn _ -> -1
```
I want to 2 things: `throw` funtion and `catch` function
* type of throw: `throw:: String -> Exc a`
```Haskell
throw :: String -> Exc a
throw = Exn
```
* type of catch: `catch :: Exc a -> (String -> a) -> a`
    * `Exn a` either give a `a` monad or an exception

```Haskell
catch :: Exc a -> (String -> a) -> a
catch eA h = case eA of
                Exn meg -> h msg
                Result x -> x
```


## count times of division
* Need state monad
* Simutenously do handle exception and counting division
* dectorer:
    * start with a function
    * wrap with more power (like cache power...)

## Transformer
* Function: monad m -> monad m', where m' behaves just like m but more powerful than m.
* Monad transformer: start with a simple monad, and continue make it more powerful.
* `evalMega :: (MonadST m, MonadExc m) => Expr -> m Int`
    * the return type is both a `MonadST` and `MonadExc`

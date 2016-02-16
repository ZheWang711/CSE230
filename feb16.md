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
* type of throw: `throw:: ` 
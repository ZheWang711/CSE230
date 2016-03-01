# Understanding Monads

* A monad is defined by three things:
    * a type constructor `m`
    * a function `reuturn :: a -> m a`
    * an operator "bind" `>>= :: m a -> (a -> m b) -> m b`

### Maybe monad



```Haskell
-- The type constructor is `m = Maybe`

return :: a -> Maybe a
return x = Just x

(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
m >>= g = case m of
            Nothing -> Nothing
            Just x -> g x
```
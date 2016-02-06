# Exercises

### Complex lambda expression derivation  
 What is the value of `quiz5`?

```Haskell
x |> f   = f x
do2 f x  = x |> f |> f
do3 f x  = x |> f |> f |> f

quiz5   = foo (+ 10) 0
  where
    foo = do2 do3
```
**Solution**:
```
|>     = \x -> \f -> f x
do2    = \f -> \x -> f (f x)
do3    = \f -> \x -> f (f (f x))
(+ 10) = \x -> x + 10
```
Then, it gets ugly, but it works out like we'd expect:
```Haskell
foo = do2 do3
 = (\f -> \x -> f (f x)) (\f -> \x -> f (f (f x)))
 = (\f -> \x -> f (f x)) (\g -> \y -> g (g (g y))) -- alpha renaming
 = \x -> f (f x) where f = (\g -> \y -> g (g (g y)))
 = \x -> (\g -> \y -> g (g (g y))) ((\g -> \y -> g (g (g y))) x)
 = \x -> (\g -> \y -> g (g (g y))) (\y -> x (x (x y)))
 = \x -> (\g -> \y -> g (g (g y))) (\z -> x (x (x z))) -- alpha renaming
 = \x -> \y -> g (g (g y)) where g = (\z -> x (x (x z)))
 = \x -> \y -> (\z -> x (x (x z))) ((\z -> x (x (x z))) ((\z -> x (x (x z))) y))
 = \x -> \y -> (\z -> x (x (x z))) ((\z -> x (x (x z))) ((\w -> x (x (x w))) y)) -- alpha renaming
 = \x -> \y -> (\z -> x (x (x z))) ((\z -> x (x (x z))) (x (x (x y))))
 = \x -> \y -> (\z -> x (x (x z))) (x (x (x (x (x (x y))))))
 = \x -> \y -> x (x (x (x (x (x (x (x (x y)))))))) -- note: "foo x y" applies "x" 9 times to "y"
quiz5 = foo (+ 10) 0
 = (\x -> \y -> x (x (x (x (x (x (x (x (x y))))))))) (\x -> x + 10) 0
 = (\x -> \y -> x (x (x (x (x (x (x (x (x y))))))))) (\z -> z + 10) 0 -- alpha renaming
 = \y -> (\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) y)))))))) 0
 = (\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) 0))))))))
 = (\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) ((\z -> z + 10) (0 + 10))))))))
 = ...
 = 0 + 10 + 10 + 10 + 10 + 10 + 10 + 10 + 10 + 10
 = 90
```
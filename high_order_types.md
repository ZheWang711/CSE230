# High order types

### Functor

```Haskell
class Functor f where
  fmap :: (a -> b) -> f a -> f b

-- Tree is an instance of functor
instance Functor Tree where
  fmap f (Leaf x) = Leaf (f x)
  fmap f (Branch t1 t2) = Branch (fmap t1)(fmap t2)
  
-- list is an instance of functor
instance Functor [] where
  fmap f [] = []
  fmap f (x:xs) = f x : fmap f xs
```

* We can now use the same name `fmap` for list, trees and other instances of `Functor`
  * Couldn't have been done without high-order type constructors (generic "container" types)
* Laws: Ensure that the shape of "contain type" is unchanged by `fmap`, and the contents are not rearranged by mapping function.
  * `fmap id = id` where `id = \x -> x`
  * `fmap (f.g) = fmap f . fmap g` 

### Monad: generalization of the principles that underline IO
```Haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b
  return :: a -> m a
  
  -- default (>>) :: m a -> m b -> m b
  -- default fail :: String -> m a
```

* `do` syntax is actually shorthand for use of monadic operators!
  * `do e => e`: something like `do putStr "Hello Word" is equvalent to `putStr "Hello World"
  * `do e1; e2; ...; en => eq >> do e2; ... en`
    * For example, combining this rule with the previous one:  
      ```Haskell
      do writeFile "testFile.txt" "Hello File System"
         putStr "Hello World"
      
      -- is equvalent to
      
      writeFile "testFile.txt" "Hello File System" >> putStr "Hello World"
      ```
    *  In the above example, Sequencing of two commands is just the application of `(>>)` to the two values of type `IO()`. The entire expression is of type `IO()`.
  * **Rules for pattern matching:**
    ```Haskell
    do pat <- e1; e2;...en  =>
    
    let ok pat = do e2;...en
        ok _ = fail "..."
    in e1 >>= ok
    ```
    * Think `e1 >>= ok` as: It "executes" `e1`, then applies `ok` to the result
    * What happens after that is defined by `ok`, if the match succeeds, the rest of the commands executed, otherwise the operation `fail` in the monad class is called, which in most cases resylts in an error.
    * A special case: `pat` is just a name, the mathing cannot fail, the rule simplifies to:
    ```Haskell
    do x <- e1; e2; ...; en   =>
    
    e1 >>= \x -> do e2; ...; en
    ```
  * Final rule: `let` notation in a `do` expression
    ```Haskell
    do let decllist; e2; ... en   =>
    
    let decllist in do e2; ...; en
    ```
 
 

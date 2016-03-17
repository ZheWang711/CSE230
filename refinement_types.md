# Refinement Types

#### Refinement Types = Types + Logical Predicates

* Examples of expression
```Haskell
x + y - z
2 * x
1 + size x
```

* Examples of Predicates
```Haskell
x + y <= 3
null x
x < 10 ==> y < 10 ==> x + y < 20
0 < x + y <=> 0 < y + x
```

* Semantics
  * **implication:** `==>`
  * **If-and-only-if:** `<=>`
  * **Environment:** mapping from variables to their Haskell types, e.g. `x :: Int`
  * **Assignment** under an environment: mapping from variables to values of the types specified in the environment, e.g. `x := 1`
  * **Predicate Evaluates** to either True or False under a given assignment
    * **Satisfiable in an enviroment:** if there exists an assignemnt that makes the predicate evaluate to True.
    * **Predicate is Valid in an environment:** if every assignment in that environment makes the predicate evaluate to True

### Examples

```Haskell
-- Prop v = (v == True)
{-@ type TRUE = {v:Bool | Prop v} @-}
{-@ type FALSE = {v:Bool | not (Prop v)} @-}

-- predicate that using variables
{-@ ex1 :: Bool -> TRUE @-}
ex1 b = b || not b

--- check if a predicate is valid
{-@ ex3 :: Bool -> Bool -> TRUE @-}
ex3 a b = (a && b) ==> a

{-@ ex4 :: Bool -> Bool -> TRUE @-}
ex4 a b = (a && b) ==> b

{-@ ax0 :: TRUE @-}
ax0 = 1 + 1 == 2

-- uninterpeted function -----------------

-- axiom of congruence
{-@ congruence :: Int -> Int -> TRUE @-}
congruence x y = (x == y) ==> (f x == f y)


{-@ measure size @-}
size        :: [a] -> Int
size []     = 0
size (x:xs) = 1 + size xs

{-@ fx0 :: [a] -> [a] -> TRUE @-}
fx0 xs ys = (xs == ys) ==> (size xs == size ys)

{-@ fx2 :: a -> [a] -> TRUE @-}
fx2 x xs = 0 < size ys
  where
    ys   = x : xs
    
fx2VC x xs ys =   (0 <= size xs)
              ==> (size ys == 1 + size xs)
              ==> (0 < size ys)
```
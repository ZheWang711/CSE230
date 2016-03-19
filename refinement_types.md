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

### `measure` and `inline`

In both cases, the body must be expressions that are:
* **Valid refinements** whose grammar was shown in the the first lecture.
* In particular, the only function symbols are OTHER measures or inlines etc OR symbols understood by the SMT solver (e.g. `<, >, +, -`)

A `measure` is a single argument function from a **data type** to something else, with single equation per data constructor.

`measure` example:
```Haskell
{-@ measure len @-}
len :: [a] -> Int
len [] = 0
len (x:xs) = 1 + xs
```

An `inline` is an arbitrary non recursive function (multiple parameters)

`inline` example:
```Haskell
{-@ inline max @-}
max :: Int -> Int -> Int
max x y = if x > y then x else y
```


### Case Study: Associative Maps & Evaluation

1. Let's Banish Missing Key Exceptions!
```Haskell

data Map k v = Emp
             | Bind k v (Map k v)
               deriving (Eq, Ord, Show)


{-@ measure keys @-}
keys :: (Ord k) => Map k v -> S.Set k
keys Emp          = S.empty
keys (Bind k _ m) = add k m

{-@ empty :: {m:Map k v | noKeys m} @-}
empty :: Map k v
empty = Emp

{-@ inline noKeys @-}
noKeys :: (Ord k) => Map k v -> Bool
noKeys m = keys m == S.empty

{-@ insert :: k:_ -> _ -> m:_ -> {v: _ | keys v = add k m } @-}
insert :: k -> v -> Map k v -> Map k v
insert k v m = Bind k v m

{-@ inline add @-}
add :: (Ord k) => k -> Map k v -> S.Set k
add k kvs = S.union (S.singleton k) (keys kvs)

{-@ lookup :: (Eq k) => k:k -> {m:Map k v | has k m} -> v @-}
lookup k' (Bind k v m)
  | k' == k   = v
  | otherwise = lookup k' m
lookup _  Emp = impossible "lookup"

{-@ inline has @-}
has :: (Ord k) => k -> Map k v -> Bool
has k m = S.member k (keys m)    -- EXERCISE fix using,
                  --   keys     :: Map k v -> Set k
                  --   S.member :: k -> S.Set k -> Bool
```

2. Expressions
```Haskell
-- Let's define a small language of Expr
data Var  = V String deriving (Eq, Ord, Show)
data Expr = Val  Int
          | Var  Var
          | Plus Expr Expr
          | Let  Var  Expr Expr
          
-- Now we can deine values as a refinement of Expr
{-@ type Val = {v:Expr | isVal v} @-}
{-@ measure isVal @-}
isVal :: Expr -> Bool
isVal (Val _) = True
isVal _       = False

-- Q: What's a suitable signature for plus?
{-@ plus :: Val -> Val -> Val @-}
plus (Val i) (Val j) = Val (i+j)
plus _         _     = impossible "plus"

-- Environments: Env maps Var to Val
{-@ type Env = Map Var Val @-}

-- Free Variables
{-@ measure free @-}
free               :: Expr -> S.Set Var
free (Val _)       = S.empty
free (Var x)       = S.singleton x
free (Plus e1 e2)  = S.union xs1 xs2
  where xs1        = free e1
        xs2        = free e2
free (Let x e1 e2) = S.union xs1 (S.difference xs2 xs)
  where xs1        = free e1
        xs2        = free e2
        xs         = S.singleton x
        
-- Well-scoped expressions: e is well-scoped in an eng G if free variables of e are defined in G
{-@ type ScopedExpr G = {e: Expr | wellScoped G e} @-}

{-@ inline wellScoped @-}
wellScoped :: Env -> Expr -> Bool
wellScoped g e = S.isSubsetOf (free e) (keys g)

-- we can now eval Expr as:
{-@ eval :: env:Env -> ScopedExpr{env} -> Val @-}
eval _ i@(Val _)     = i
eval g (Var x)       = lookup x g
eval g (Plus e1 e2)  = plus (eval g e1) (eval g e2)
eval g (Let x e1 e2) = eval gx e2
  where
    gx               = insert x v1 g
    v1               = eval g e1

-- Experise: Top-;level Evaluation:
=-   A closed Expr can be evaluated in any environment
{-@ type ClosedExpr = {e: Expr | closed e} @-}
{-@ topEval :: ClosedExpr -> Val @-}
topEval =  eval Emp

{-@ inline closed @-}
closed :: Expr -> Bool
closed e = S.isSubsetOf (free e) (S.empty) -- EXERCISE
-- Here, we can't use "closed e = wellScoped Emp e" since SMT solver cannot recognize wellScoped


-- safeEval should work with any Expr and Env
{-@ safeEval :: Env -> Expr -> Maybe Val @-}
safeEval g e
  | ok        = Just $ eval g e
  | otherwise = Nothing
  where
    ok        = wellScoped g e  -- EXERCISE
```

















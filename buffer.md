# class note Feb 9

## Solve the monad applicative error
```Haskell
import Control.Applicative
import Control.Monad (liftM, ap)

instance Functor ST0 where
  fmap = liftM

instance Applicative ST0 where
  pure  = return
  (<*>) = ap
```


* `type Parser = String -> (StructuredObject, String) ` what is the output string? why not `type Parser = String -> StructuredObject`?  The bit of remaining string!
Only a part of of string is rendered at a time, want to output composible objects!

* expression:
    
    ```Haskell
    data Expr = Number Inyt
              | Plus Expr Expr
    
    -- "2+1+5" is:
    -- (Plus(Plus (Number 2) (Number 3)) (Number 5))
    -- (Plus (Number 2) (Plus (Number 3) (Number 5)))
    ```
    
    
```Haskell
    -- a new operator $
    
    "2$3$5"
    data Expr = Nuumber Int
              | Dollar Expr Expr
              
    
    -- (Dollar(Dollar (Number 2) (Number 3)) (Number 5))
    -- (Dollar (Number 2) (Dollar (Number 3) (Number 5)))
    
        ```

```Haskell
type X = ... -- not creating a new type, jus a short hand
data X = ... -- creates a new data type
newtype X = ... -- also creates a new data type
```

```Haskell
-- take as input a parser, and a string s
doParse :: Parser a -> String -> [(a, String)]
doParse (P p) s = p s
```

* Quiz 1,

```Haskell
Which of the following is a valid single-character-parser that returns the first Char from a string (if one exists.)
-- a
oneChar = P $ \cs -> head cs
-- same as oneChar = P (\cs -> head cs)
-- b
oneChar = P $ \case -> {[] -> [('', []) | c:cs -> (c, cs)}

-- c
oneChar = P $ \cs -> (head cs, tail cs)

-- d
oneChar = P $ \cs -> [(head cs, tail cs)]

-- e
oneChar = P $ \case -> { [] -> [] | cs -> [(head cs, tail cs)]}
```
abc is out because of the wrong output type(not a list), e is correct.

* Quiz 2

```Haskell
-- I want a migical operator, such that:
-- twoChar = migicOp oneChar oneChar


twoChar' = foo oneChar oneChar 
What must the type of foo be?

Parser (Char, Char)
Parser Char -> Parser (Char, Char)
Parser a -> Parser a -> Parser (a, a)
Parser a -> Parser b -> Parser (a, b)
Parser a -> Parser (a, a)

```

Answer D is more generic!


How to write the migical operator?
```Haskell
pairP ::  Parser a -> Parser b -> Parser (a, b)
pairP p1 p2 = P (\cs -> 
  [((x,y), cs'') | (x, cs' ) <- doParse p1 cs, 
                   (y, cs'') <- doParse p2 cs']
  )

```


```Haskell
instance Functor Parser
instance Applicative Parser
instance Monad Parser where
  -- return :: a -> Parser a
  return x = P (\cs -> [(x, cs)])
  
  -- (>>=) :: PArser a -> (a -> Parser b) -> Parser b
  (>>=) = bindP
  bindP :::: PArser a -> (a -> Parser b) -> Parser b
  bindP (P pA) fB = P(\cs -> [(xB, cs'') | (xA, cs') <- pA cs]
                                          , let P pb = fB xA
                                          , (XB, cs'') <- pB cs' ])

```

* failP
```Haskell
failP :: Parser 
failP = P(\_ -> [])
-- instead we can use const
failP = P $ const []
```

```Haskell
digitInt = do c <- digitChar
              return ((read [c]) :: Int)
```

```Haskell
strP :: string -> Parser String
strP [] = P (\cs -> ("", cs))
-- strP (c:cs) = return ""
strO (c:cs) = undefined
-- c== is the same as \x -> x == c
```

* Quiz

```Haskell
-- a 
p1 `chooseP` p2 = do xs <- p1
                     ys <- p2
                     return (x1 ++ x2) 
-- b
p1 `chooseP` p2 = do xs <- p1 
                     case xs of 
                       [] -> p2 
                       _  -> return xs

-- c
p1 `chooseP` p2 = P $ \cs -> doParse p1 cs ++ doParse p2 cs

-- d
p1 `chooseP` p2 = P $ \cs -> case doParse p1 cs of
                               [] -> doParse p2 cs
                               rs -> rs
```




for a and b if the first parser failed, the whole failed.
c: add all the possible answers.
d: try the first one, if it succeed, use it, otherwise backtrack and run the second one

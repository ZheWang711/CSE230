# Feb11

```Haskell

instance Functor PArser where
    -- fmap :: (a -> b) -> Parser a -> Parser b
    fmap f (P pa) = P (\cs -> [(xb, cs') | (xa, cs')  <- pa cs  ])

instance Applicative Parser
    --pure :: a -> Parser a
    pure x = P (\cs -> [(x, cs)])
    
    --(<*>) :: Parser (a -> b) -> Parser a -> Parser b
    (P fP) <*> (P xP) = P (\cs -> [(xb, cs'') | (f, cs') <- fP cs
                                            (x, cs'') <- xP cs'])
    
    
instance Monad PArser where


take4P :: Parser String
take4P = do
    x1 <- oneChar
    x2 <- oneChar
    x3 <- oneChar
    x4 <- oneChar
    return [x1, x2, x3, x4]

-- generlize    
takeNP :: Int -> Parser String
takeNP 0 = return ""
takeNP n = do
    x <- oneChar
    xs <- takeNP (n-1)
    return (x:xs)
    
-- take out the recursion, using map!
map f [] = []
map f (x:xs) = f x : map f xs

{-
oneChar oneChar oneChar ... oneChar

Take all the results, and give back the results as one big list

[Parser Char] -> Parser [Char]


1. repeat an action "n" times
    Int -> Parser a -> [Parser a]
    generalize: Int -> b -> [b]
    
2. gather the result of sequence actions
    [Parser a] -> Parser [a]

-}

takeNP :: Int -> Parser String
takeNP n = sequence (replicate n oneChar)
--takeNP n = replicateM n oneChar


take2or4 = (takeNP 2)  `choose` (takeNP 4)

    
```
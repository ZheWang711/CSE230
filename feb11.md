# Feb11

```Haskell
take4P :: Parser String
take4P = do
    x1 <- oneChar
    x2 <- oneChar
    x3 <- oneChar
    x4 <- oneChar
    return [x1, x2, x3, x4]
    
takeNP :: Int -> Parser String
takeNP 0 = return ""
takeNP n = do
    x <- oneChar
    xs <- takeNP (n-1)
    return (x:xs)
    
```
# High Order Functions 1

### Functions are data

* You can pass functions around to in any manner that you can pass any other data around in.
    * e.g. make a pair of functions
    * e.g. make a list of functions

#### *High order functions*: take other functions as input and return functions as output.

* Taking functions as input
```Haskell
do twice f x = f (f x)
```
* Return functions as output
```Haskell
plusn :: Int -> (Int -> Int)
plusn n = f where f x = x + n
```

* Partial application

`->` is a type operator that takes 2 types: input and output and returns a new function type.
* `->` is  right-associative: `Int -> Int -> Int` is equivalent to `Int -> (Int -> Int)`.
* 

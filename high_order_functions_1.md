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

* `->` is a type operator that takes 2 types: input and output and returns a new function type.  
`->` is  right-associative: `Int -> Int -> Int` is equivalent to `Int -> (Int -> Int)`.

```Haskell
plus :: Int -> Int -> Int
plus n x = n + x

plusn :: Int -> (Int -> Int)
plus n = f where f x = x + n
```

`plusn` is equivalent to the partially applied `plus n`

**EX:**

What is the value of `zog 100`?
```Haskell
plus n x = n + x
doTwice f x = f (f x)
zog = doTwice(plus 20)
```
$$plus = \lambda n.\lambda x. n + x\\
doTwice = \lambda f.\lambda x. f(f x)\\
zog\quad100=  \lambda f.\lambda x. f(f x) (\lambda n.\lambda x. n + x(20)\quad100)\\
=\lambda f.\lambda x. f(f x) (\lambda x. 20 +x\quad100)\\
=(\lambda x. 20 +x)(\lambda x. 20 +x(100))\\
=(\lambda x. 20 +x)(120)\\
=140$$




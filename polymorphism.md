# Polymorphism

* What is polymorphism? -- Abstraction that allows function works with different kinds of values.

**EX**

```Haskell
doTwice f x = f (f x) -- parenthesis here should not be omitted
``` 
Then the type of `doTwice` is `(t -> t) -> t -> t`, the point is that the first argument should be a function with same input and output type, which make the `doTwice` operation possible.

### Polymorphic data structures

* Polymorphic functions: can operate on different kinds values.
* Polymorphic data structures: can contain different kinds of values.
* Polymorphic functions often associate with polymorphic data structures, e.g. `[]` -- list data structure and `len` -- length of a list.

### Bottling computation patterns with polymorphic high-order functions

#### Iteration:
```Haskell
map f [] = []
map f (x:xs) = (f x) : (map f xs)

toUpperString = map toUpper
```
* In Haskell, `f x = e x` is identical to `f = e` **as long as `x` doesn't appear in `e`**

#### Folding:
```Haskell
foldr op base [] = base
foldr op base (x:xs) = x `op` (foldr op base xs)


listAdd = foldr (+) 0
listMul = foldr (*) 1
```


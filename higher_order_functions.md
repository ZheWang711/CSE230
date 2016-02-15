# Higher Order Functions

## Curried Functions
* Currying is the process of transforming a function that takes multiple arguments into a function that takes just a single argument and returns another function if any arguments are still needed.
* Every function in Haskell officially only takes one parameter
* All functions that accepted several parameters so far have been **curried functions**
    * `max 4 5` is equivalent with `(max 4) 5`
    * Putting **space** between 2 things is simply function application, **highest precedence**.
    * `max :: (Ord a) => a -> a -> a` can also be written as `max :: (Ord a) => a -> (a -> a)`
    * Doing `max 4 5` first creates a function that takes a parameter and returns either 4 or that parameter, depending on which is bigger. Then, 5 is applied to that function and that function produces our desired result.
* **Partially applied** -- call a function with too few parameters
    * Neat way to create function on the fly
    * Section -- a way to partially apply an infix function: by using parentheses and only supply a parameter on one side.
    ```Haskell
    divideByTen :: (Floating a) => a -> a
    divideByTem = (/10)
    ```
        * **Special case: (-4) means minus 4**, if you want to make a function that substract 4 from the number:`(substract 4)`

## Some higher-orderism is in order

* Functions can take functions as parameters and also return functions.
* Unlike imperative programming (use for loops; while loops;), **functional programming uses higher order functions to **
* We can take advantage of currying when making higher-order functions by thinking ahead and writing what their end result would be if they were called fully applied.

Ex: Implement function `zipWith`, which takes a function and 2 lists as parameters, and join the lists by applying the function between corresponding elements of 2 lists.
```Haskell
zipWith' :: (a -> b -> c) -> [a] -> [b] -> [c]
zipWith' _ [] _ = []
zipWith' _ _ [] = []
zipWith' f (x:xs) (y:ys) = f x y : zipWith' f xs ys
```

Ex: Implement `flip` function, which takes a function and returns a function that is like original function, only the first 2 arguments are flipped.
```Haskell
flip' :: (a -> b -> c) -> (b -> a -> c)
flip' f = g 
        where g x y = f y x
-- we can make the implementation even simplier!
-- since (a -> b -> c) -> (b -> a -> c) is same as (a -> b -> c) -> b -> a -> c
-- we can take advantage of currying when making higher-order 
-- functions by thinking ahead and writing what their end result would be if they were called fully applied.
flip'' :: (a -> b -> c) -> b -> a -> c
flip'' f y x = f x y
```

## Maps and filters
* `map` takes a function and a list and applies that function to every element in the list, producing a new list.
```Haskell
map :: (a -> b) -> [a] -> [b]
map _ [] = []
map f (x:xs) = f x : map f xs
```
* Each of the usages can be achieved using list comprehension
    * `map (+3) [1,5,3,1,6]` is same as `[(+3) x | x <- [1,5,3,1,6]]`
    * Using map is much more readable for cases where you only apply some function to the elements of a list, **especially once you're dealing with maps of maps**.

* `filter` is a function that takes a predicate (a function that tells whether something is true or not).
```Haskell
filter :: (a -> Boll) -> [a] -> [a]
fileter _ [] = []
filter p (x:xs)
    | p x = x : filter p xs
    | otherwise = filter p xs
```
* Each of the usages can also be achieved with list comprehensions by the use of predicates.
* Applying several predicates in a list comprehension == filtering something several times == joining the predicates with the logical && function.
* Thanks to Haskell's laziness, even if you map something over a list several times and filter it several times, it will only pass over the list once.
* `takeWhile predicate list` function takes `predicate` and a `list`, then goes from the beginning of the `list` and returns its elements while the `predicate` holds true. 
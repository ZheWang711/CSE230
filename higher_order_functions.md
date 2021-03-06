# Higher Order Functions

## Curried Functions
*  Currying is the technique of translating the evaluation of a function that takes multiple arguments (or a tuple of arguments) into evaluating a sequence of functions, each with a single argument. It is automatically done by Haskell.
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
* Can also apply functions that takes multiple parameters in `map`
    * `map (*) [0..5]` get a list of functions: `[(0*), (1*), ... (5*)]`

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
* `filter` doesn't work on infinite lists, but `takeWhile` does.

## Lambdas
* Basically anonymous functions that are used because we need some function only once.
* Normally for the sole purpose of passing it to a high order function
* `\ parameter1 parameter2 -> function body`
* One Parameter cannot have several patterns

```Haskell
flip' :: (a -> b -> c) -> b -> a -> c  
flip' f = \x y -> f y x  
```

## Folds
* Reduce the list to a single 
* Takes a binary function, a starting value, and a list to fold up.
* Right folds work on infinite lists, whereas left fold don't!
* **Folds can be used to implement any function where you traverse a list once, element by element, and the return something based on that** --- Whenever you want to traverse a list to return something, chances are you want a fold.
* `scanl` and `scanr` are like `foldl` and `foldr`, only they report all the intermediate accumulator states in the form of a list. 
    * When you use a `scanl`, the final result will be in the last element of resulting list whereas `scanr` will place the result on the head!

E.X:  How many elements does it take for the sum of the roots of all natural numbers to exceed 1000?
```Haskell
sqrtSums :: Int
sqrtSums = length (takeWhile (<1000) (scanl1 (+)  (map (sqrt) [1,2..]))) + 1
-- same as length (takeWhile (<1000) (scanl (+) 0 (map (sqrt) [1,2..])))
```

## Function application with `$`
* `$` function also called function application
* `$` has the lowest precedence -- right associative
    * `sqrt 3 + 4 + 9` is same as `(sqrt 3) + 4 + 9`
    * `sqrt $ 3 + 4 + 9` is same as `sqrt (3 + 4 + 9)`
* `$` means that function can be treated just like another function:
    * `map ($ 3) [(4+), (10*), (^2), sqrt]` is `[7.0,30.0,9.0,1.7320508075688772]  `

## Function composition using `.`
* In Haskell, we do function composition with `.` function:
```Haskell
(.) :: (b -> c) -> (a -> b) -> a -> c
f . g = \x -> f (g x)
```
* Can make function on the fly to pass to other functions (clearer and more concise than lambda expression)
E.X: turn a list of numbers to negative numbers

```Haskell
-- use lambda expression
map (\ x -> negate (abs x)) [5,-3,-6,7,-3,2,-19,24]

-- use function composition
map (nogate . abs) [5,-3,-6,7,-3,2,-19,24]
```

* Right associative, so we can compose many functions at a time.  
` map (negate . sum . tail) [[1..5],[3..6],[1..7]]`
* How to compose functions that take several parameters? Partially apply!
* If you want to rewrite an expression with a lot of parentheses by using function composition, you can start by putting the last parameter of the innermost function after a `$` and then just composing all the other function calls, writing them without their last parameter and putting dots between them. 
    * `sum (replicate 5 (max 6.7 8.9))` is same as `(sum . replicate 5 . max 6.7) 8.9` is same as `sum . replicate 5 . max 6.7 $ 8.9`
    * `replicate 100 (product (map (*3) (zipWith max [1,2,3,4,5] [4,5,6,7,8])))` is same as `replicate 100 . product . map (*3) . zipWith max [1,2,3,4,5] $ [4,5,6,7,8]` -- the functions end with 3 `)`, so there is 3 `.`
    * Write function in point free style  
    `fn x = ceiling (negate (tan (cos (max 50 x)))) ` can be written as `fn = ceiling . negate . tan . cos . max 50`
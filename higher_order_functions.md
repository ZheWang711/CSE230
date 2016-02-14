# Higher Order Functions

## Curried Functions

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
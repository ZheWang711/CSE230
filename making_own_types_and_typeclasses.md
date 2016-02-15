# Making Own Types and Typeclasses

## Algebraic data types intro
* `data` meas we are defining a new data type
    * ``data Bool = False | True
    * Part before `=` denotes the type
    * Parts after `=` are **value constructors**
* When we write a value constructor, we can optionally add some types after it and those types define the value it will contain.
    * e.g. `data Shape = Circle Float Float Float | Rectangle Float Float Float Float`
* Value constructors are actually functions that ultimately return a value of a data type.
    * e.g `Circle :: Float -> Float -> Float -> Shape`
* Make a function takes a shape are returns its surface
    ```Haskell
    surface :: Shape -> Float
    surface (Circle _ _ r) = pi * r ^ 2
    surface (Rectangle x1 x2 y1 y2) = (abs $ x2 - x1) * (abs $ y2 - y1)
    ```
    * we couldn't write a type declaration of `Circle -> Float` because `Circle` is not a type, `Shape` is.
    * **We can pattern matched against constructors -- just write a constructor and then bind its fields to names**
    * When we try to print a value out in the prompt, Haskell first runs `show` function to get the string representation of our value and then it prints that out to the terminal.
        * to make `Shape` type part of the `Show` typeclass  
        ```Haskell
        data Shape = Circle Float Float Float | Rectangle Float Float Float Float deriving (Show)
        ```
* Value constructors are functions, we can partially apply them and everything. `map (Circle 10 20) [4,5,6,6]`

* Intermediate data type
    * Common to use same name of constructor as type if there is only one constructor
    ```Haskell
    data Point = Point Float Float deriving (Show)  
    data Shape = Circle Point Float | Rectangle Point Point deriving (Show)
    
    surface :: Shape -> Float  
    surface (Circle _ r) = pi * r ^ 2  
    surface (Rectangle (Point x1 y1) (Point x2 y2)) = (abs $ x2 - x1) * (abs $ y2 - y1 
    
    nudge :: Shape -> Float -> Float -> Shape  
    nudge (Circle (Point x y) r) a b = Circle (Point (x+a) (y+b)) r  
    nudge (Rectangle (Point x1 y1) (Point x2 y2)) a b = Rectangle (Point (x1+a) (y1+b)) (Point (x2+a) (y2+b))  
    
    baseCircle :: Float -> Shape  
    baseCircle r = Circle (Point 0 0) r  
  
    baseRect :: Float -> Float -> Shape  
    baseRect width height = Rectangle (Point 0 0) (Point width height)
    
    -- if you don't want to deal with intermediate type, Point, just construct a baseCircle / baseRect and nudge it!
    
    ```
* Export data types in your modules -- write your type along with the functions you are exporting and then add some parentheses and in them specify the value construcots that you want to export for it, separated by commas.
    * Export all the value constructors for a given type -- write `..`
```Haskell
module Shapes   
( Point(..)  
, Shape(..)  
, surface  
, nudge  
, baseCircle  
, baseRect  
) where  
```
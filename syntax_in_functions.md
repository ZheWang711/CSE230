# Syntax in functions

## Pattern Matching -- making sure a value conforms to some form and deconstructing it

* Pattern Matching: Pattern matching consists of specifying patterns to which some data should conform and then checking to see if it does and deconstructing the data according to those patterns.

* When defining functions, you can define separate function bodies for different patterns

* You can pattern match on any data type.
* Patterns will be **checked from top to bottom** (order is important), when it conform to a pattern, the corresponding function body will be used.

* Pattern matching may fail ==> `Non-exhaustive patterns`
*  Pattern matching used on tuples:
    ```Haskell
    addVectors :: (Num a) => (a, a) -> (a, a) -> (a, a)  
    addVectors (x1, y1) (x2, y2) = (x1 + x2, y1 + y2)  
    ```
* Tuple pattern matching in list comprehensions
    ```Haskell
    let xs = [(1,3), (4,3), (2,4), (5,3), (5,6), (3,1)]  
    [a+b | (a,b) <- xs] -- [4,7,6,8,11,4]   
    ```
    Should a pattern match fail, it will just move on to the next element.


* List pattern matching
    * You can match with the empty list `[]` or any pattern that involves `:` and the empty list.
    * `x:xs` will bind the head of the list to `x` and the rest of it to `xs`, even if one element so `xs` ends up being an empty list (Since `[1,2,3]` is just syntactic sugar for `1:2:3:[]`)
        * `x:xs` will match against lists of length 1 or more 


---



* **If you want to bind to several variables (even if one of them is just _ and doesn't actually bind at all), we have to surround them in parentheses.** `head' (x: _)  = x`

* `error` function takes a string and generates a runtime error. `error "Can't call head on an empty list, dummy!"` It causes the program to crash.

* Naming a pattern -- usually to avoid repeating typing the whole pattern again
    * e.g. `capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]`


* Can't use `++` in pattern matches -- it doesn't make sense


## Guards -- testing whether some property of a value are true or false

* Indicated by pipes that follow a function's name and its parameters, indented a bit to the right and lined up
* `otherwise` is the last guard in many times.
* **For a function, if all the guards of a function evaluate to `False` (no `otherwise`), then evaluation falls through to the next pattern**
* Can be written inline, but not readable.

Ex: Defining our compare function -- can be defined using backticks
```Haskell
mycompare :: (Ord a) => a -> a -> Ordering
a `mycompare` b
    | a > b = GT
    | a == b = EQ
    | otherwise = LT
```

## `where` bindings
 
* **`where` section** 
    * let you bind to variables at the end of a function and the whole function can see them
    * Names we define in the where section of a function are only visible to that function. (bindings in `where` are not shared by functions)
    * All names are aligned at a single column
    * **Can also use where bindings to pattern match in where section**!
    ```Haskell
    ...
    where bmi = weight / height ^ 2  
    (skinny, normal, fat) = (18.5, 25.0, 30.0)  
    ```
    2 ways of pattern matching -- in parameter / where section
    ```Haskell
    -- where section
    initials :: String -> String -> String
    initials firstname lastname = [f] ++ ". " ++ [l] ++ "."
    where
        (f:_) = firstname
        (l:_) = lastname
    
    -- parameter
    initials' :: String -> String -> String
    initials' (f:_) (l:_) = [f] ++ ". " ++ [l] ++ "."
    ```
    * **Can also define functions n where section!**  
    E.g. a function that takes a list of weight-height pairs and returns a list of BMI.
    ```Haskell
    calcBmis :: (RealFloat a) => [(a, a)] -> [a]
    calcBmics xs = [bmi w h | (w, h) <- xs]
        where bmi weight height = weight / height ^ 2 -- function defined in where section
    ```
    
    * where binds can also be nested, it's a common idiom to make a function and define some helper function in its *where* clause, and then give those functions helper functions as well, each with its *own where* clause.


EX: BMI calculator
```Haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | bmi <= skinny = "You're underweight, you emo, you!"  
    | bmi <= normal = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= fat    = "You're fat! Lose some weight, fatty!"  
    | otherwise     = "You're a whale, congratulations!"  
    where bmi = weight / height ^ 2 
          skinny = 18.5  
          normal = 25.0  
          fat = 30.0  
```



## **`let in` bindings**
* `let <bindings> in <expression>` form, let part are accessible to expression after in part. 
* Let in bindings let you bind to variables anywhere.
* Don't span across guards, only in expression.
* Can be used for pattern matching
    * Quick way to dismantling a tuple: `(let (a,b,c) = (1,2,3) in a+b+c) * 100 ` 
* Names in let part are also aligned in a single column (like where section)
* Can also be used to introduce functions in a local scope
    * `[let square x = x * x in (square 5, square 3, square 2)]`
* To bind several variables inline, separate then with semicolons
    * `(let a = 100; b = 200; c = 300 in a*b*c, let foo="Hey "; bar = "there!" in foo ++ bar)` 
* Use inside list comprehension `xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2]`
    * Omitted in part in list comprehension, since the visibility of names is already predefined there.
    * The in part can also be omitted when defining functions and constants directly in GHCi (visible throughout the entire session).

**Let bindings are expressions themselves, where binds are syntactic constructs!
**

## `case` expressions

* `case` expressions are expressions!
* Can do pattern matching (pattern matching in parameters in function definitions -- is only a syntactic sugar for case expressions)
* Syntax:
```Haskell
case expression of pattern -> result  
                     pattern -> result  
                     pattern -> result  
                   ...  
```
The first pattern that matches the expression is used.
* If it falls through the whole case expression and no suitable pattern is found, a runtime error occurs.
* Useful for pattern matching against something in the middle of an expression.

Interchangeable!
```Haskell
head' :: [a] -> a  
head' [] = error "No head for empty lists!"  
head' (x:_) = x 

head' :: [a] -> a  
head' xs = case xs of [] -> error "No head for empty lists!"  
                      (x:_) -> x  
```

Use case expression in function
```Haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ case xs of [] -> "empty."  
                                               [x] -> "a singleton list."   
                                               xs -> "a longer list." 
```

















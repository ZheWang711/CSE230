# Syntax in functions

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
    * 
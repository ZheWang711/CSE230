# Haskell Programming Basics

* ghci
    * `:load foo.hs`
    * `:type expression`
    * `:info variable`



### Functions
* Parenthesis can wrap compound expressions, must do so for **arguments**  
``

* Basic Types:
    * Integer
    * Double
    * Char
        * `'a'` is a Char, but `"a"` is [Char]
    * Bool
* Function Types
    * `A->B` **Function** taking input A, yielding output B
    * Multi-Argument: `A1->A2->A3->B` taking arguments A1,A2,A3, giving out B
* Tuples `(A1,A2,...,An)`: **Bounded** Sequence of values of type A1,...An
    * *Pattern matching* extracts values from tuple  
        ```Haskell
        pat::(Int, Int, Int) -> Int
        (x, y, z) = x * (y + z)
        ```
* Lists `[A]` **Unbounded Sequence** of values of types A.
    * Lists' values must have same type
    * Use `:` to construct lists
        * `(:):: a -> [a] -> [a] `
    * Use `l1++l2` to append list l2 at the end of list l1
    * Use `L!!i` to access the i+1 th element in list L (L[i])

* Define function with multiple guards -- more readable
```Haskell
    range :: Int -> Int -> [Int]
    range i j | i >= j = []
              | True = i : range (i+1) j
    
 ```
 
### Creating Types

* Type Synonyms -- not a new type, just shorthand
    * Use `type` keyword
    * not a new type, just an shorthand(alias)

* Define new types
    * **Data Types and constructors must start with capital letter**
    * Types and constructors can use the same name
    * one type can have multiple constructors

    ```Haskell
    data CircleT = Circle(Double, Double, Double)
    data SquareT = Square(Double, Double, Double)
    areaSquare :: CircleT -> Double
    areaCircle(Circle(_,_,r)) = pi * r * r
    areaSquare(Square(_,_,d)) = d * d
    c = Circle(1,2,3)
    areaCircle(c)  -- OK
    areaSquare(c)  -- type error
    ```
* Build a list with squares and circles?
    ```Haskell
    data CorS = 
        | Circle(Double, Double, Double)
        | Square(Double, Double, Double)
    area :: CorS -> Double
    ```
* Example Computing polygon area

```Haskell

import Prelude -- module that include sqrt

data Shape = Triangle Side Side Side | Polygon [Vertex] deriving Show
type Side = Double
type Vertex = (Double, Double)

distance :: Vertex -> Vertex -> Double
distance (x1, y1) (x2, y2) = d where -- we should not use any CAPITAL Character as the start of any identifier.
    d = sqrt ((x2 - x1)*(x2 - x1) + (y2 - y1)*(y2 - y1))

area :: Shape -> Double
area(Triangle a b c)  = sQ where
        p =  (a+b+c) / 2
        sQ = sqrt (p*(p-a)*(p-b)*(p-c))

-- we use Polygon (v1:v2:v3:vs) to show that we treat v1:v2:v3:vs as a single input list
area (Polygon (v1:v2:v3:vs)) = sQ where 
        a = distance v1 v2
        b = distance v2 v3
        c = distance v1 v3
        sQ = area(Triangle a b c) + area(Polygon (v1:v3:vs) )
area (Polygon _) = 0
```

### I/O
* Basic Concepts:
    * Result of a program == the value of the name *main* in the module *Main* 
    * **action**: a special kind of **value**, when a Haskell system evaluates an expression that yields an action, it knows *not* to display the resilt in standard output area, but rather to "take appropriate action".
        * primitive actions: e.g. write a single char to a file / receive a single char from keyboard
        * compound actions: e.g. print the entire string into a file
        * *commmands*: haskell expressions that evaluate to actions.
        * command type: `IO T` or `IO ()`: command return value of type `T` or no useful value returned

* `putStr::String->IO()`: takes input string, returns action that writes string to std out
* **Only one way to "execute" action**: make it the value of name `main`  
  ```
  main::IO()
  main = putStr "Hello World! \n"
  ```
* `do` Denote the beginning of a sequence of commands
    ```Haskell
    hehehe :: IO()
    hehehe = do   putStr "Hello world"
                    putStr "Hello world"
                    putStr "\n"
    ```
* `getLine :: IO String`
    * Name result via assignment `x <- act`
    * EX  
    ```Haskell
    main = do putStrLn "What is your name?"
                n <- getLine
                putStrLn("Happy New year" ++ n)
    ```
* Write to a file
    * `writeFile :: FilePath -> String -> IO()`
    * `type FilePath = String`
* Read the entire file
    * `do s <- readFile "testFile.txt"`
    * `putStr s`
* A list of actions
    * `sequence_ :: [IO a] -> IO()` turning a list of actions (values) into commands (expressions)
    * `main = sequence_ actionList`

    
    

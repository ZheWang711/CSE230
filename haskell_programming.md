# Haskell Programming

* ghci
    * `:load foo.hs`
    * `:type expression`
    * `:info variable`



### Functions
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
    
    

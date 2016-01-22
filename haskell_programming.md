# Haskell Programming

* ghci
    * `:load foo.hs`
    * `:type expression`
    * `:info variable`



### Types
* Basic Types:
    * Integer
    * Double
    * Char
    * Bool
* Function Types
    * `A->B` **Function** taking input A, yielding output B
    * Multi-Argument: `A1->A2->A3->B` taking arguments A1,A2,A3, giving out B
* Tuples `(A1,A2,...,An)`: **Bounded** Sequence of values of type A1,...An
    * *Pattern matching* extracts values from tuple
        * ```Haskell
        pat::(Int, Int, Int) -> Int
        (x, y, z) = x * (y + z)
        ```
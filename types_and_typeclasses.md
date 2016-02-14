# Types and Typeclasses

## Believe the type
* A type is a kind of label that every expression has, itr tells us in which category of things that expression fits.
* Types are written in **CAPITAL CASE**.
* Haskell has a static type system, the type of every expression is known at compile time.
* Unlike Java, Haskell has type inference
* If you want to give your function a type declaration but are unsure as to what it should be, you can always just write the function without it and then check it with `:t`.
* Common types:
    * `Int`
    * `Integer`: no bounded `Int`(can be very big), `Int` is more efficient
    * `Float`: floating point with single precision
    * `Double`: a real floating point with double precision
    * `Bool`: Boolean type, can only be `True` or `False`
    * `Char`: single character, denoted by single quotes

## Type variables and Type Classes

* Remember `head` is a function that return the first element of a list: `:t head` ==> `[a] -> a`
    * `a` here is not a type (Written in capital), it is a **type variable**.
    * allows use to write general functions
    * **polymorphic functions:** functions that have type variables


* A Type class is sort of interface that defines some behavior.
* **If a type is part of  a typeclass**, it **support and implements the behavior** the typeclass describes. (Kind of like Java interfaces, only better)
* 
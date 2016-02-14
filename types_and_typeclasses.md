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
* If a function is comprised only of special characters, it's considered a infix function by default
* `=>` symbol
    * Everything before `=>` is called a class constraint.
    * `(==) :: (Eq a) => a -> a -> Bool` -- the equality function takes 2 values that are of same type and returns a `Bool`. The type of those 2 values must be a member of `Eq` class.
    * `Eq` is a type class. Any type where it makes sense to test for equality between 2 values of that type should be a member of `Eq` class.
    * The `elem` function has a type of `(Eq a) => a -> [a] -> Bool` because it uses `==` over a list to check whether some value we're looking for is in it.


* Some basic typeclasses
    * `Eq` is used for types that support equality testing. 
        * Its members implements `==` or `/=`
    * `Ord` is for types that have an ordering.
        * to be a member of `Ord`, a type must first have membership in `Eq`
        * The `Compare` function takes 2 `Ord` members and returns an `Ordering` (can be `GT`, `LT`, `EQ`)
    *  `Show` members can be represented as strings.
        * Mostly dealt by `show` function, takes a value whose type is a member of  `Show` and represents it to us as a string     
    * `Read` (sort of opposite type class of `Show`)
        * `read` function takes a string and returns a type which is a member of Read `read :: (Read a) => String -> a  `
        * **type annotations** -- a way of explicitly saying what the type of an expression should be. `read "5" :: Int`
    * `Enum` members are sequentially ordered types, we can use its types in list ranges
        * `succ()` and `pred()`
        * types in this class: (), Bool, Char, Ordering, Int, Integer, Float and Double
    * `Bounded` members have a upper and lower bound: `minBound :: Int`. (Polymorphic constants)
    * `Num` is a numeric typeclass, its members have the property of being able to act like numbers (also polymorphic constants)
        * to join `Num`, a type must already be friends with `Show` and `Eq`
        * `Integral` is also a numeric class, include all integral numbers (`Int` and `Integer`)
        * `Floating` includes only floating point numbers (`Float` and `Double`)
        * Useful function for dealing with numbers `fromIntegral`
            * `fromIntegral :: (Integral a, Num b) => a -> b`
            * takes an integral number and turns it into a more general number
            * E.g. `fromIntegral (length [1,2,3,4]) + 3.2`





















    
# Starting Out


## Summary
* Use range to generate list: e.g. `[20,19..0]`
* List comprehension: `[1 | _ <- xs]`
* Combine 2 lists: `zip`


### Lists

* A string is a list of characters
* Elements should be same type
* Lists can contain lists, can be different length but cannot be different types
* Operations:
    * `++` for concatenate (putting 2 lists together)
    * `:` for adding a element in the front of a list
        * `[1,2,3]` is actually `1:2:3:[]`
    * `!!` for indexing
    * `head x` return the first element of a list `x`
    * `tail x` return `x` without the first element
    * `last x` return the last element of a list `x`
    * `init x` return `x` without the last element
    * `length x` return `x`'s length
    * `null x` return if x is empty
    * `reverse x` reverses a list `x`
    * `take num list` extracts `num` of elements from the beginning of the `list`
    * `drop num list` drops `num` of elements from the beginning of the `list`
    * `minimum` returns the smallest
    * `maximum` returns the biggest
    * `sum` returns the sum of a list of elements
    * `product` return the product of a list of elements
    * `elem thing list` tell us if `thing` is an element of the list
    * Can be compared if the elements can be compared, in **lexicographical order**

### Ranges
* `[1..20]` is a list of numbers from 1 to 20
* specifying step: `[2,4..20]` a list of numbers 2,4,6,8,...20
* descending order: `[20, 19..1]`
* using floating point in ranges => may have some unexpected results => don't use them!
* Can use range to make infinite lists, by just not specifying an upper limit
    * e.g. to get first 24 multiples of 13: `take 24 [13, 26..]`
* 2 functions that produce infinite lists:
    * `cycle list` takes a `list` and cycles into an infinite list
    * `repeat elem` takes an `elem` and produces an infinite list of that element
* `replicate num elem` if you want `num` of `elem` in a list


### List Comprehension
* **Basic:** `[x*2 | x <- [1..10]]` to get `[2,4,6,8,10,12,14,16,18,20]`, same as `take 10 [2,4..]`
* **Add a condition:** `[x*2 | x <- [1..10], x*2 >= 12]`
* **Filtering:** `boomBangs xs = [if x < 10 then "BOOM!" else "BANG!" | x <- xs, odd x]`
* **Use several predicates:** `[x | x <- [10..20], x /= 13, x /= 15, x /= 19]`
    * To be included in the result, an element must satisfy all the predicates
* **Draw from several lists**: produce all combinations of given lists, then join them by the output function we supply
    * `[x*y | x <- [2,5,10], y <- [8,10,11]]`


Example 1: write down our own version of length, call it `length'`
```Haskell
-- The plan here is to replace each element with 1
-- then compute the sum
length' xs = sum [1 | _ <- xs]
```

Example 2: remove non uppercase from a string
```Haskell
-- Since a string is a list of character
-- we can use list comprehension
removeNonUppercase st = [c | c <- st, c `elem` ['A'..'Z']]
```

* **Nested list comprehension** -- operating list that  
Example 3: remove all odd numbers without flattening the list
```Haskell
let xxs = [[1,3,5,2,3,1,2,4,5],[1,2,3,4,5,6,7,8,9],[1,2,4,2,1,6,3,1,3,2,3,6]] 
[[x | x <- xs, even x] | xs <- xss]
-- the above result will be [[2,2,4],[2,4,6,8],[2,4,2,6,2,6]]  
```

### Tuples

* Use tuples when you know in advance how many components some piece of data should have.
* No need homogeneous, a tuple can contain a combination of several types
* Tuples can be used to represent a wide variety of data.
    * `("Christopher", "Walken", 55)`
* Tuples can contain lists
* No singleton tuple: a singleton tuple would just be the value it contains.
* Tuples can be compared with each other if their components can be compared (oly if same length).
* 2 use functions operation operate on pairs:
    * `fst` takes a pair and return its first component
    * `snd` takes a pair and return its second component.
* Produce pairs using `zip`
    * takes 2 lists and then zips them together into one list, bvy joining the matching elements into pairs.
    ```Haskell
    zip [1 .. 5] ["one", "two", "three", "four", "five"]  
    --will return [(1,"one"),(2,"two"),(3,"three"),(4,"four"),(5,"five")]  
    ```
    * The longer list simply gets cut off to match the length of the shorter one (*laziness*).
    ```Haskell
    zip [1..] ["apple", "orange", "cherry", "mango"]  
    -- will return [(1,"apple"),(2,"orange"),(3,"cherry"),(4,"mango")]  
    ```
Example 4: Which right triangle that has integers for all sides and all sides equal to or smaller than 10 has a perimeter of 24?

```Haskell
-- first, generate al possible side combinations
-- to aviod repeated answer, ordering a < b < c
-- use predicates to apply constraints
let rightTriangles = [(a, b, c) | c <- [1..10], b <- [1..c], a <- [1..b], a^2 + b^2 == c^2, a + b + c == 24]
```




























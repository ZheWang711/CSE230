# CSE230
Programming Languages

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

###
* Can be compared if the elements can be compared, in **lexicographical order**
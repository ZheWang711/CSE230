# Recursive Data Type

### Haskell recursively defining datatype
```Haskell
data IntList = IEmpty
             | IOneAndMore Int IntList
             deriving (Show)

is1 = IOneAndMore 1 IEmpty
is2 = IOneAndMore 2 is1
is3 = IOneAndMore 3 is3
```

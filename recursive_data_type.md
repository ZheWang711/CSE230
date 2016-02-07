# Recursive Data Type

### Recursively defining datatype in Haskell

* **Int list**

```Haskell
data IntList = IEmpty
             | IOneAndMore Int IntList
             deriving (Show)

is1 = IOneAndMore 1 IEmpty
is2 = IOneAndMore 2 is1
is3 = IOneAndMore 3 is3
```
* **Type definition can contain multiple Recursive occurrences**, like binary tree:

```Haskell
data IntTree = ILeaf Int
             | INode IntTree IntTree
             deriving(Show)

```
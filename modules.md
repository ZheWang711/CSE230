# Modules

* A Haskell module is a collection of related functions, types and typeclasses.
* A Haskell program is a collection of modules where the** main module loads up the other modules** and then uses the functions defined in them to do something.


## Loading Modules
* `import <module name>` must be done before defining any functions.  
EX: When you do `import Data.List`, all the functions that `Data.List` exports become available in global namespace (you can call then wherever in the script).
* In GHCI, you can load up modules by `ghci> :m + Data.List Data.Map Data.set`
* **Just need a couple of functions from a module:** `import Data.List (nub, sort)`
* **import all function of a module except for a few select ones:** `import Data.List hiding (nub)`
* **Qualified imports -- deal with name clashes:** `import qualified Data.Map`
    * Reference `Data.Map.filter` for the `filter` function in 
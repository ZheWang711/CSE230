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
    * Reference `Data.Map.filter` for the `filter` function in `Data.Map`
    * `filter` still refers to the functions in `Data.Map`
* **Renaming qualified module:** `import qualified Data.Map as M`
    * Refer `M.filter` for the `flter` function in `Data.Map`

## Common Modules
* `Data.List`
* `Data.Char`
* `Data.Map`
* `Data.Set`

TODO: 此处待补全

## Making our own modules

* Good to take functions and types that work towards a similar purpose and put them in a module -- easily reuse them by importing your module!
* When making a module, we usually export those functions that act as a sort of interface to our modules so that the implementation is hidden, and we even can delete them in another version!
* The module `hs` file should be in the same folder with the program that's importing it in.

**Example: a toy module for calculating the volume and area of a few geometrical objects**
```Haskell
-- file Geometry.hs (same as module name)

-- no "rectangleArea" function here
module Geometry
( sphereVolume
, sphereArea
, cubeVolume
, cubeArea
, cuboidArea
, cuboidVolume
) where

sphereVolume :: Float -> Float
sphereVolume radius = (4.0 / 3.0) * pi * (radius ^ 3)

sphereArea :: Float -> Float
sphereArea radius = 4 * pi * (radius ^ 2)

cubeVolume :: Float -> Float
cubeVolume side = cuboidVolume side side side

cubeArea :: Float -> Float
cubeArea side = cuboidArea side side side

cuboidVolume :: Float -> Float -> Float -> Float
cuboidVolume a b c = rectangleArea a b * c

cuboidArea :: Float -> Float -> Float -> Float
cuboidArea a b c = rectangleArea a b * 2 + rectangleArea a c * 2 + rectangleArea c b * 2

-- Since this is a helper function, we don't present it in the head!
rectangleArea :: Float -> Float -> Float
rectangleArea a b = a * b
```

**Defining sub modules!**
* Make a folder called `Geometry`, place 3 files: `Sphere.hs`, `Cuboid.hs` and `Cube.hs`.
    * `Sphere.hs`
    ```Haskell
    module Geometry.Sphere
    ( volume
    , area
    ) where
    
    volume :: Float -> Float
    volume radius = (4.0 / 3.0) * pi * (radius ^ 3)
    ```
    * `Cuboid.hs`
    ```Haskell
    module Geometry.Cuboid
    ( volume
    , area
    ) where
    
    volume :: Float -> Float -> Float -> Float
    volume a b c = rectangleArea a b * c
    
    area :: Float -> Float -> Float -> Float
    area a b c = rectangleArea a b * 2 + rectangleArea a c * 2 + rectangleArea c b * 2
    
    rectangleArea :: Float -> Float
    rectangleArea a b = a * b
    ```
    * `Cube.hs`
    ```Haskell
    module Geometry.Cube
    ( volume
    , area
    ) where
    
    import qualified Geometry.Cuboid as Cuboid
    
    volume :: Float -> Float
    volume side = Cuboid.volume side side side
    
    area :: Float -> Float
    area side = Cuboid.area side side side
    ```
































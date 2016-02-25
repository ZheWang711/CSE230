# IO

* IO action -- something that when performed, will carry out an action with a side-effect, and also contain some kind of return value inside it.
* Use `do` syntax to glue together several I/O actions into one.
* `name <- getLine` -- **perform the I/O action `getLine`** and then bind its result value to `name`.
    * The only way to get the data inside an action is to use `<-` construct
    * If we're taking data out of an I/O action, we can only take it out when we're inside another I/O action  
    **limited tainted Example**
    ```Haskell
    main = do
        putStrLn "Hello, what's your name?"
        name <- getLine
        putStrLn $ "Read this carefully, because this is your future: " ++ tellFortune name
        -- here tellFortune doesn't have to know anything about I/O, 
        -- it's just a normal String -> String function
    ```
    * `nameTag = "Hello, my name is " ++ getLine` doesn't work since the type mismatch (`String` with `IO String`), need to get the result of the I/O action first!
        * If you want to deal with impure data, you have to do it in an impure environment!
        
* In `do` block, **the last action cannot be bound to a name** -- automically extracts the value from the last action and binds it to its own result.
*  I/O actions will only be performed when they are given a name of `main` or when they're inside a bigger I/O action that we composed with a `do` block. They'll be performed only if they eventually fall into main.
*  Let bindings `let bindings in expression` in `do` blocks: the `in` part isn't needed.
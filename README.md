# Writing Reusable Code in Julia

### Use case

-   You are working on a research project that requires computation. 💻👩‍💻

-   You write your code; you test it (👍) and it just works! 🚀

-   You decide your code is general enough that it could be useful for solving related problems. 🤓

-   You add some documentation and host your code in some public repository and tell other people about it. 🎤

-   Other people realize your code could be useful to solve their problems! 😎

-   They take your code, try to use it and... it doesn't work 😱

-   If they're lucky, the code "might work" but the results are not the expected ones 😱😱

This is a very common problem in the scientific research community. Today's workshop is an introduction about best practices to building reusable code in the Julia ecosystem.

## Our definition of reusable code

[Reusable code](https://en.wikipedia.org/wiki/Reusability) is code that is easy to distribute and use (correctly and consistently).

You'll need Git, VS Code (recommended) with the Julia extension and Julia (naturally).

To write reusable Julia code we need to understand how to *manage dependencies* in Julia.

## Managing Dependencies

A dependency (dep) is code other people wrote and we are using in project.

Let's add some deps to our base Julia installation using Julia's Package Manager.

1.  Add a few deps with the `add` command

    ``` julia
    $ julia
    julia> # get to pkg mode with `]`
    (@v1.9) pkg> add Example StaticArrays
    ```

2.  Look at the deps in your base Julia

    ``` julia
    (@v1.9) pkg> status
    Status `C:\Users\cpaniagu\.julia\environments\v1.9\Project.toml`
    [7876af07] Example v0.5.3
    [90137ffa] StaticArrays v1.6.3
    ```

3.  Remove a dep with `rm`

    ``` julia
    (@v1.9) pkg> rm StaticArrays
    ```

4.  Seeing is believing: look at your deps again

    ``` julia
    (@v1.9) pkg> st # and be lazy
    Status `C:\Users\cpaniagu\.julia\environments\v1.9\Project.toml`
    [7876af07] Example v0.5.3
    ```

## Make a package!

A package is code with structure that leverages available tooling for efficient distribution and use. We could add this structure to our code manually but there is tooling for this already! 🛠️

Some options

-   Use `Pkg.generate` for a bare-bones package structure
-   Use [PkgTemplates.jl](https://github.com/JuliaCI/PkgTemplates.jl) for more features (Readme, License, Documentation, test suite, CI and more)
-   Clone some base repo and customize to your needs ([Example.jl](https://github.com/JuliaLang/Example.jl), [MyAwesomePackage.jl](https://github.com/sylvaticus/MyAwesomePackage.jl))

For the rest of the workshop we'll use `generate` from the Julia package manager.

1.  Navigate to where you want to your project/package.

2.  From Julia's package manager issue `generate MyPackage`

    ``` julia
     (@v1.9) pkg> generate MyPackage
    ```

    You will see a new folder `MyPackage` was created.

    ```         
    $ cd MyPackage
    $ tree
    .
    ├── Project.toml
    └── src
        └── MyPackage.jl
    ```

3.  Let's add some deps and code to our package by editing `src/MyPackage.jl`

    ``` julia
    module MyPackage

    using Example: domath # added this dep

    fact() = println("FYI, 2 + 5 = \$(domath(2))") # added this new feature

    export fact

    end 
    ```

4.  Let's test our new features! 🤞

    ``` julia
    (@v1.9) pkg> activate .
      Activating project at `C:\Users\cpaniagu\Documents\Reusable-code-Julia\MyPackage`
    julia> using MyPackage
    [ Info: Precompiling MyPackage [934d1629-71ee-47e4-906a-ddb3ea0dd61f]
    ERROR: LoadError: ArgumentError: Package MyPackage does not have Example in its dependencies:
    - You may have a partially installed environment. Try `Pkg.instantiate()`
      to ensure all packages in the environment are installed.
    - Or, if you have MyPackage checked out for development and have
      added Example as a dependency but haven't updated your primary
      environment's manifest file, try `Pkg.resolve()`.
    - Otherwise you may need to report an issue with MyPackage
    [More error feedback here]
    ```

    Julia is suggesting that we have an undocumented/uninstalled/broken dependency. Here we just need to add it to our project. Let's do it! 
    ```julia
    (MyPackage) pkg> add Example
       Resolving package versions...
        Updating `C:\Users\cpaniagu\Documents\Reusable-code-Julia\MyPackage123\Project.toml`
      [7876af07] + Example v0.5.3
        Updating `C:\Users\cpaniagu\Documents\Reusable-code-Julia\MyPackage123\Manifest.toml`
      [7876af07] + Example v0.5.3
    Precompiling project...
      1 dependency successfully precompiled in 1 seconds. 1 already precompiled.
    ```
    ```julia
    (MyPackage) pkg> st # again seeing is believing
    Project MyPackage123 v0.1.0
    Status `C:\Users\cpaniagu\Documents\Reusable-code-Julia\MyPackage123\Project.toml`
    [7876af07] Example v0.5.3
    ```

    Now that the `Example` dep is documented and compiled let's try testing our package again! 
    ```julia
    julia> using MyPackage

    julia> fact()
    FYI, 2 + 5 = 7

    julia>
    ```
Success! Our feature works and we are ready (sort of) to share our package with the world!

### Recommended Configuration

1.  Add a `.JuliaFormatter.toml` file with the following 
    ```toml
    style = "blue"
    ```

2.  Add a `.gitignore` file 
    ```
    .JuliaFormatter.toml
    ```

## Publish your package!
Use tooling within VS Code (Source Control panel) for the following
1.  Make the project a git repository, commit changes.
2. Push your changes to a remote repository.

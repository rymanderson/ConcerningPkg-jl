# Concerning `Pkg.jl`...

```
AUTHOR  : Ryan Anderson
DATE    : 06-22-2020
PURPOSE : An easy guide to making/using packages in `Julia`.
```

## Transforming a project to a package

Say you have a project that you want to make a package. There are several tools available to automatically create a new package, described [here](https://julialang.github.io/Pkg.jl/v1/creating-packages/).


### Dos and Don'ts

First, you should __NEVER__ manually create __OR__ edit your `Manifest.toml` file. This file is meant to show a reproducible state of your project, e.g. the specific commits of projects used, etc. Note that this __ONLY WORKS__ if you have `]add`'ed the package (the `]` character is used to enter the package manager in the julia REPL). If you have `dev`'ed the package, `Pkg.jl`references only the path to your repo, and it won't update your `Manifest.toml` file, or change the contents of your package in any manner.

### Why?

The idea is someone can use your `Project.toml` and `Manifest.toml` files and easily re-create the exact state of your project. Even if you don't use registered julia packages, `Manifest.toml` tracks the precise git commit of a repo that you have added to your project (see `Step 6` of the next section).

### Steps

But, you can and should create a `Project.toml` file. Follow these steps:

1. get a project uuid

    ```julia
    ]add UUIDs
    UUIDs.uuid4()
    ```

2. create a file in the top directory of your project called `Project.toml` and fill out the following info:

    ```julia
    name = "CoolNameHere"
    uuid = "3b2d03ab-9272-44f7-b407-c4eb53025f1c" # this is the UUID you got in step 1
    author = ["Cool Developer Guy <cool.email@some.server"]
    version = "0.0.1" # or whatever version you want 
    ```

3. alternatively, you could have run `]generate CoolNameHere`, but it might be more convenient to follow `1-2` if you already have a directory structure
4. alternatively again, you could have used the package `PkgTemplates.jl`
5. run `]activate` to activate the project

    * now, when you enter the package manager, you should see your project name in parentheses in the REPL.

6. now run `]add PackageDependency1 PackageDependency2...`

    * To add a specific branch or commit from a git repository, add `#` and either a branch name or the commit hash:

        ```julia
        ]add MyPackage1#master
        ]add MyPackage2#cf6ba6cc0be0bb5f56840188563579d67048be34
        ```
    

## The `Project.toml` file

Here are some tricks. Check the [docs](https://julialang.github.io/Pkg.jl/v1/toml-files/) for more details.

Note that `6.` adds packages to your `Project.toml` file under the heading `[deps]`. You shouldn't have to edit this section at all by hand, except to cut/paste dependencies to the `[extras]` heading if they are only needed for unit tests in `runtests.jl`.

### `[compat]`

Your `Project.toml` file will contain a section that looks [like](https://github.com/JuliaDiff/ForwardDiff.jl/blob/master/Project.toml):

```julia
[compat]
Calculus = "0.2, 0.3, 0.4, 0.5"
CommonSubexpressions = "0.1, 0.2"
DiffResults = "0.0.1, 0.0.2, 0.0.3, 0.0.4, 1.0.1"
DiffRules = "0.0.4, 0.0.5, 0.0.6, 0.0.7, 0.0.8, 0.0.9, 0.0.10, 0.1, 1.0"
DiffTests = "0.0.1, 0.1"
NaNMath = "0.2.2, 0.3"
SpecialFunctions = "0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 0.10"
StaticArrays = "0.8.3, 0.9, 0.10, 0.11, 0.12"
julia = "1"
```

Note that `julia` only has `"1"` listed, meaning (I believe) all `1.x` versions are compatible.

### `[extras]`

Your `Project.toml` file may also contain an `[extras]` section. So far, I've only seen this used with `[targets]`. For example, you can specify test dependencies at the end of your `Project.toml` as:

```julia
[extras]
JLD = "4138dd39-2aa7-5051-a626-17a0bb65d9c8"
PyPlot = "d330b81b-6aea-500a-939a-2ce795aea3ee"
Test = "8dfed614-e22c-5e08-85e1-65c5234f0b40"

[targets]
test = ["PyPlot", "JLD", "Test"]
```

Voila! You should now have a functional package which you can add to your `julia` environment.

## Importing Your Package

When you are ready to import your package, enter the package manager in the REPL and run:

```julia
]
activate path/to/your/package
```

to tell Julia which `Manifest.toml` file to use. Then, while still in the package manager, run

```julia
instantiate
```

to get the package versions called for your package.

## Build

This is useful if you need to build any non-julia code into your package.

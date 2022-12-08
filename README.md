Do you have a Julia package that provides standalone functionality which you would like to
share with folks who do not use Julia? This is for you.

# How do deploy with Quickdraw

Make a Julia package with a `main()` function, put it in a public github repo, and put this this in your README.md

````
# Installation using [Quickdraw](https://github.com/LilithHafner/Quickdraw)

To install this software on Linux or Mac, run the following command:

```
curl -fLsS https://lilithhafner.com/quickdraw | sh -s https://github.com/MyUsernameOrOrg/MyPackage.jl
```

To install this software on Windows, install Julia and then run the following command:
```
(echo julia -e "import Pkg; Pkg.activate(\"MyPackage\", shared=true); try Pkg.add(url=\"https://github.com/MyUsernameOrOrg/MyPackage.jl\"); catch; println(\"Warning: update failed\") end; using MyPackage: main; main()" %0 %* && echo pause) > MyPackage.bat
```

In both cases, the command will create an executable called `MyPackage` that can be double clicked to run.
````

Things to remember
- Change `MyUserNameOrOrg` to yor github username or the name of your the github organization that hosts the package being deployed. For example `LilithHafner` or `JuliaCollections`.
- Change `MyPackage` to your package name (excluding `.jl`). For example `Minesweeper` or `BouncingBall`.
- Unlike in a REPL, the application will exit when your main function exits. You can put `wait()` at the end of your `main()` function to prevent it from exiting.
- Quickdraw automatically installs Julia if it is not already installed on Mac and Linux systems. On Windows systems, the user must ensure that Julia is installed before running the Quickdraw command.

# Example

The following is found in the [README of ManualImageCoding.jl](https://github.com/LilithHafner/ManualImageCoding.jl):

---

### Installation using [Quickdraw](https://github.com/LilithHafner/quickdraw)

To install this software on Linux or Mac, run the following command:

```
curl -fLsS https://lilithhafner.com/quickdraw | sh -s https://github.com/LilithHafner/ManualImageCoding.jl
```

To install this software on Windows, install Julia and then run the following command:
```
(echo julia -e "import Pkg; Pkg.activate(\"ManualImageCoding\", shared=true); try Pkg.add(url=\"https://github.com/LilithHafner/ManualImageCoding.jl\"); catch; println(\"Warning: update failed\") end; using ManualImageCoding: main; main()" %0 %* && echo pause) > ManualImageCoding.bat
```

In both cases, the command will create an executable called `ManualImageCoding` that can be double clicked to run.

---

# Supported Systems

- MacOS is supported and tested
- Linux is supported and lightly tested
- Windows is supported and lightly tested, but you need to install Julia separately from the windows store.


# How it works

## Linux and Mac

The command above downloads [this script](script) and runs it with the locator of the
package as an argument. The script checks if Julia is installed, and if not, guides the user
through the installation process with [juliaup](https://github.com/JuliaLang/juliaup).
Then, the script creates a new shared environment at `@PackageName`, adds the package, and
loads the package, ensuring it has a main function. Finally, the script creates a second
script which will remain on the user's computer. The second script is as follows
```
julia -e 'import Pkg; Pkg.activate("PackageName", shared=true); Pkg.update(); using PackageName: main; main()' $0 $@
```
It launches julia, activates the shared environment, loads the package, and runs the main
function. This script is relocatable to anywhere within the user's computer.

## Windows

The command creates a batch file that invokes julia, installs or updates the requested package, and runs the package's main function.

# Accessing the path to the App

The package may access the path of the double-clickable app with `ARGS[1]`. If the app is
invoked from the command line, then `ARGS[2:end]` will reflect the command line arguments.

# Deploying registered packages

You may deploy registered packages just like unregistered packages using a full URL.

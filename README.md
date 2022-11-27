# Work in progress

Do you have a Julia package that provides standalone functionality which you would like to
share with folks who do not use Julia? This is for you.

# Usage

If a Julia package provides standalone functionality through a `main()` function, then
the following command will install it on any Linux or Mac, wheather or not Julia is already installed:
```
curl -fLsS https://raw.githubusercontent.com/lilithhafner/quickdraw/main/script | sh -s LOCATOR
```
And on windows the following command will install it, but Julia must be installed separately
```
(echo julia -e "import Pkg; Pkg.activate(\"PACKAGE\", shared=true); try Pkg.add(url=\"LOCATOR\") catch; println(\"Warning: update failed\") end; using PACKAGE: main; main()" %0 %* && echo pause) > PACKAGE.bat
```

LOCATOR is the url of the package or, if registered, simply the name of the package.
In the Windows version, omit `url=` for registered packages.

# Example

The following is found in the [README of ManualImageCoding.jl](https://github.com/LilithHafner/ManualImageCoding.jl):

---

## Installation

To install this software on Linux or Mac, run the following command:

```
curl -fLsS https://raw.githubusercontent.com/lilithhafner/quickdraw/main/script | sh -s https://github.com/LilithHafner/ManualImageCoding.jl
```

To install this software on Windows, install Julia and then run the following command:
```
(echo julia -e "import Pkg; Pkg.activate(\"ManualImageCoding\", shared=true); try Pkg.add(url=\"https://github.com/LilithHafner/ManualImageCoding.jl\"); catch; println(\"Warning: update failed\") end; using ManualImageCoding: main; main()" %0 %* && echo pause) > ManualImageCoding.bat
```

In all cases, the command will create an executable called `ManualImageCoding` that can be double clicked to run.

---

# Supported Systems

- MacOS is supported and lightly tested
- Windows is supported and lightly tested, but you need to install Julia separately from the windows store.
- Linux might work

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

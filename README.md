Do you have a Julia package that provides standalone functionality which you would like to 
share with folks who do not use Julia? This is for you.

# Usage

If a Julia package provides standalone functionality through a `main()` function, then
the following command will install it on any supported system

```
curl -fLsS https://raw.githubusercontent.com/lilithhafner/quickdraw/main/script | sh -s LOCATOR
```
where LOCATOR is the url of the package or, if registered, simply the name of the package.

For example, to install [ManualImageCoding.jl](https://github.com/LilithHafner/ManualImageCoding.jl), run this command
```
curl -fLsS https://raw.githubusercontent.com/lilithhafner/quickdraw/main/script | sh -s https://github.com/LilithHafner/ManualImageCoding.jl
```
This command will create an executable called `ManualImageCoding` that can be double 
clicked to run the main function of the package

# Supported Systems

- MacOS is supported and tested
- Linux might work
- Windows might work if you already have julia installed

# How it works

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

# Accessing the path to the App

The package may access the path of the double-clickable app with `ARGS[1]`. If the app is
invoked from the command line, then `ARGS[2:end]` will reflect the command line arguments.

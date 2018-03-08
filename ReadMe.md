*Disclaimer: The information below is **not to be considered the absolute truth**. It is simply what worked for me on Windows in regards to getting my Mono code to run on Ubuntu and Mac.*

# 0. Prerequisites

- Visual Studio 2017
  - When installing add: Clang and the VS SDK.
- Mono
- Knowledge of how to run terminal commands.

# 1. Downloading Target SDK

Before you start you need to download Mono SDK packages for the platforms you will be targeting. Your version of Mono, which you can check using `mono --version`, must match the package version. Pick one [here](http://download.mono-project.com/runtimes/raw) and add them using:

`mkbundle --fetch-target {target} --target-server http://download.mono-project.com/runtimes/raw/`

With Mono 5.10.0 the targets I have installed are: 

`mono-5.10.0-osx-10.7-x64` and `mono-5.10.0-ubuntu-16.04-x64`

# 2. Packaging

After your application has been built you will need to statically link it in order for the code to run without Mono installed on the user's machine. First locate where the framework is installed on your computer and find your .Net version, for me this was:

`C:\Program Files\Mono\lib\mono\4.5`

Afterwards run the following command where:

`{OutputFileName}` - Is the name of the output file. No extension is needed.

`{ExeName}` - The name of the exe to package.

`{MonoPath}` - The path you chose from the first paragraph.

`{Target}` - The name of the target you will be compiling for.

`mkbundle -o {OutputFileName} {ExeName} -L {MonoPath} --deps --static --cross {Target}`

# 3. Platform Specific Fixes

- Linux
  - An error similar to `DLLNotFound: lib.c` can be fixed by including the System.dll.config file from this repo with your application.
  - Terminal applications will not open the terminal unless opened from the terminal. I have no solution for this other than invoking Linux stuff to open it yourself.
- Mac
  - File might open as a text file when double-clicked. In cases like this running `chmod +x {FileName}` worked.

# 4. Notes

- Some libraries like `ServiceStack.Text` will not work when packaged for some reason. Look for alternatives.
- When choosing the MonoPath in step 2 look out for folders ending in `-api` like `4.7-api`. They do not work as expected.

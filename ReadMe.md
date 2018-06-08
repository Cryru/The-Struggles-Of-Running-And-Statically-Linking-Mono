*Disclaimer: The information below is **not to be considered the absolute truth**. It is simply what worked for me on Windows in regards to getting my Mono code to run on Ubuntu and Mac.*

# 0. Prerequisites

- Visual Studio 2017
  - When installing add: Clang and the VS SDK.
- Mono
- Knowledge of terminal commands and computers.

# 1. Downloading Target SDK

Before you start you need to download Mono SDK packages for the platforms you will be targeting. Your version of Mono, which you can check using `mono --version`, must match the package version. Pick one [here](http://download.mono-project.com/runtimes/raw) and add them using:

`mkbundle --fetch-target $target --target-server http://download.mono-project.com/runtimes/raw/`

where `$target` is the name of the target you want to add. On Mono 5.10.0 the targets I have installed are: 

`mono-5.10.0-osx-10.7-x64` and `mono-5.10.0-ubuntu-16.04-x64`

# 2. Packaging

After your application has been built you will need to statically link it in order for the code to run without Mono installed on the user's machine. First locate where the framework is installed on your computer and find your .Net version, for me this was:

`C:\Program Files\Mono\lib\mono\4.5`

Afterwards run the following command where:

`$OutputFileName` - Is the name of the output file. No extension is needed.

`$ExeName` - The name of the exe to package.

`$MonoPath` - The path you located in the first paragraph.

`$Target` - The name of the target you will be compiling for.

`mkbundle -o $OutputFileName $ExeName -L $MonoPath --deps --static --cross $Target`

Optionally you can add a machine.config to ensure environment similiary by adding the following argument to the command above. 

`--machine-config $config`

The config is usually found in `%windir%\Microsoft.NET\Framework64\$version\config\machine.config` for 64-bit systems or `%windir%\Microsoft.NET\Framework\$version\config\machine.config` for 32-bit systems.

For example: `C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config`.

**Note:** Some .NET versions use the same CLR, so for instance version `v3.0` and `v3.5` will not contain a machine.config - they will use the one present in version `2.0.50727`.

**Tip:** You can easily find your config by running the `[System.Runtime.InteropServices.RuntimeEnvironment]::SystemConfigurationFile` command in PowerShell.

# 3. Platform Specific Fixes

- Linux
  - An error similar to `DLLNotFound: lib.c` can be fixed by including the System.dll.config file from this repo with your application.
  - Terminal applications will not open the terminal unless opened from the terminal. I have no solution for this other than invoking Linux stuff to open a console window yourself.
- Mac
  - File might open as a text file when double-clicked. In cases like this running `chmod +x {FileName}` worked.
  - Sometimes the dllmap file will not work when the application is double-clicked but will work when launched from the console from the current folder, resulting in `DLLNotFound` exceptions. It will however not work if launched from another folder. The fix I use for this is to include a `.command` file which will `cd` to the current folder and launch the application. I've included a template for such a script in this repo under the name `MacRunScriptTemplate.command`. Don't forget Windows line endings as those will break the script on Mac.

# 4. Notes

- Some libraries like `ServiceStack.Text` will not work when packaged for some reason, this can happen when libraries invoke unmanaged code or are generally not path agnostic. I have no solution for this, look for alternatives.
- When choosing the MonoPath in step 2 look out for folders ending in `-api` like `4.7-api`. They do not work as expected.
- If you receive an error when launching your application **like** `Unexpected character ")"` this means you've packaged for the wrong platform. Check your `--cross` argument from step 2. Things like x86 and x64 can play a factor.
  - The Linux (and Windows with cygwin) command `file {path}` can give you insight into what you've just packaged.
- Sometimes your `.dll.config` file correctly points to the platform library but execution will still claim it cannot find it. This happens when your unmanaged library requires another library which is missing. To debug this use the `MONO_LOG_LEVEL=debug mono $Exe.exe` command where `$Exe` is the compiled **non-bundled** executable.
  
 # 5. Useful Pages and Projects
 
 ### MonoKickStart
 
 https://github.com/OutOfOrder/MonoKickstart
 
 Something like the included `MacRunScriptTemplate.command` but **VERY** expanded.
 
 ### Mono MKBundle and Linking Documentation
 
 http://www.mono-project.com/docs/tools+libraries/tools/mkbundle/
 http://www.mono-project.com/docs/advanced/pinvoke/dllnotfoundexception/
 
 ### Example
 
 https://github.com/mrts/mono-static-linking

# Using CMake with FreeRTOS<a name="getting-started-cmake"></a>

You can use CMake to generate project build files from FreeRTOS application source code, and to build and run the source code\.

You can also use an IDE to edit, debug, compile, flash, and run code on FreeRTOS\-qualified devices\. Each board\-specific Getting Started guide includes instructions for setting up the IDE for a particular platform\. If you prefer working without an IDE, you can use other third\-party code editing and debugging tools for developing and debugging your code, and then use CMake to build and run the applications\.

The following boards support CMake:
+ Espressif ESP32\-DevKitC
+ Espressif ESP\-WROVER\-KIT
+ Infineon XMC4800 IoT Connectivity Kit
+ Marvell MW320 AWS IoT Starter Kit
+ Marvell MW322 AWS IoT Starter Kit
+ Microchip Curiosity PIC32MZEF Bundle
+ Nordic nRF52840 DK Development kit
+ STMicroelectronicsSTM32L4 Discovery Kit IoT Node
+ Texas Instruments CC3220SF\-LAUNCHXL
+ Microsoft Windows Simulator

See the topics below for more information about using CMake with FreeRTOS\.

**Topics**
+ [Prerequisites](#building-cmake-prereqs)
+ [Developing FreeRTOS applications with third\-party code editors and debugging tools](#developing-third-party)
+ [Building FreeRTOS with CMake](#building-cmake)

## Prerequisites<a name="building-cmake-prereqs"></a>

Make sure that your host machine meets the following prerequisites before continuing:
+ Your device's compilation toolchain must support the machine's operating system\. CMake supports all versions of Windows, macOS, and Linux

  Windows subsystem for Linux \(WSL\) is not supported\. Use native CMake on Windows machines\.
+ You must have CMake version 3\.13 or higher installed\.

  You can download the binary distribution of CMake from [CMake\.org](https://cmake.org/download/)\.
**Note**  
If you download the binary distribution of CMake, make sure that you add the CMake executable to the PATH environment variable before you using CMake from command line\.

  You can also download and install CMake using a package manager, like [homebrew](https://brew.sh/) on macOS, and [scoop](https://scoop.sh/) or [chocolatey](https://chocolatey.org/) on Windows\.
**Note**  
The CMake package versions provided in the package managers for many Linux distributions are out\-of\-date\. If your distribution's package manager does not provide the latest version of CMake, you can try alternative package managers, like `linuxbrew` or `nix`\.
+ You must have a compatible native build system\.

  CMake can target many native build systems, including [GNU Make](https://www.gnu.org/software/make/) or [Ninja](https://github.com/ninja-build/ninja/releases)\. Both Make and Ninja can be installed with package managers on Linux, macOS and Windows\. If you are using Make on Windows, you can install a standalone version from [Equation](http://www.equation.com/servlet/equation.cmd?fa=make), or you can install [MinGW](https://sourceforge.net/projects/mingw-w64/files/), which bundles make\.
**Note**  
The Make executable in MinGW is called `mingw32-make.exe`, instead of `make.exe`\.

  We recommend that you use Ninja, as it is faster than Make and also provides native support to all desktop operating systems\.

## Developing FreeRTOS applications with third\-party code editors and debugging tools<a name="developing-third-party"></a>

You can use a code editor and a debugging extension or a third\-party debugging tool to develop applications for FreeRTOS\.

If, for example, you use [Visual Studio Code](https://code.visualstudio.com/) as your code editor, you can install the [Cortex\-Debug](https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug) VS Code extension as a debugger\. When you finish developing your application, you can invoke the CMake command\-line tool to build your project from within VS Code\. For more information about using CMake to build FreeRTOS applications, see [Building FreeRTOS with CMake](#building-cmake)\.

For debugging, you can provide a VS Code with debug configuration similar to the following:

```
"configurations": [
    {
        "name": "Cortex Debug",
        "cwd": "${workspaceRoot}",
        "executable": "./build/st/stm32l475_discovery/aws_demos.elf",
        "request": "launch",
        "type": "cortex-debug",
        "servertype": "stutil"
    }
]
```

## Building FreeRTOS with CMake<a name="building-cmake"></a>

CMake targets your host operating system as the target system by default\. To use it for cross compiling, CMake requires a toolchain file, which specifies the compiler that you want to use\. In FreeRTOS, we provide default toolchain files in `freertos/tools/cmake/toolchains`\. The way to provide this file to CMake depends on whether you’re using the CMake command line interface or GUI\. For more details, follow the [Generating build files \(CMake command\-line tool\)](#cmake-gen-cli) instructions below\. For more information about cross\-compiling in CMake, see [CrossCompiling](https://gitlab.kitware.com/cmake/community/wikis/doc/cmake/CrossCompiling) in the official CMake wiki\.

**To build a CMake\-based project**

1. Run CMake to generate the build files for a native build system, like Make or Ninja\.

   You can use either the [CMake command\-line tool](https://cmake.org/cmake/help/latest/manual/cmake.1.html) or the [CMake GUI](https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html) to generate the build files for your native build system\.

   For information about generating FreeRTOS build files, see [Generating build files \(CMake command\-line tool\)](#cmake-gen-cli) and [Generating build files \(CMake GUI\)](#cmake-gen-gui)\.

1. Invoke the native build system to make the project into an executable\.

   For information about making FreeRTOS build files, see [Building FreeRTOS from generated build files](#cmake-build)\.

### Generating build files \(CMake command\-line tool\)<a name="cmake-gen-cli"></a>

You can use the CMake command\-line tool \(cmake\) to generate build files for FreeRTOS\. To generate the build files, you need to specify a target board, a compiler, and the location of the source code and build directory\. 

You can use the following options for cmake:
+ `-DVENDOR` – Specifies the target board\.
+ `-DCOMPILER` – Specifies the compiler\.
+ `-S` – Specifies the location of the source code\.
+ `-B` – Specifies the location of generated build files\.

 

**Note**  
The compiler must be in the system's `PATH` variable, or you must specify the location of the compiler\.

For example, if the vendor is Texas Instruments, and the board is the CC3220 Launchpad, and the compiler is GCC for ARM, you can issue the following command to build the source files from the current directory to a directory named `build-directory`:

```
cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S . -B build-directory
```

**Note**  
If you are using Windows, you must specify the native build system because CMake uses Visual Studio by default\. For example:  

```
cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S . -B build-directory -G Ninja
```
Or:  

```
cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S . -B build-directory -G "MinGW Makefiles"
```

The regular expressions `${VENDOR}.*` and `${BOARD}.*` are used to search for a matching board, so you don't have to use the full names of the vendor and board for the `VENDOR` and `BOARD` options\. Partial names work, provided there is a single match\. For example, the following commands generate the same build files from the same source:

```
cmake -DVENDOR=ti -DCOMPILER=arm-ti -S . -B build-directory
```

```
cmake -DBOARD=cc3220 -DCOMPILER=arm-ti -S . -B build-directory
```

```
cmake -DVENDOR=t -DBOARD=cc -DCOMPILER=arm-ti -S . -B build-directory
```

You can use the `CMAKE_TOOLCHAIN_FILE` option if you want to use a toolchain file that is not located in the default directory `cmake/toolchains`\. For example:

```
cmake -DBOARD=cc3220 -DCMAKE_TOOLCHAIN_FILE='/path/to/toolchain_file.cmake' -S . -B build-directory
```

If the toolchain file does not use absolute paths for your compiler, and you didn't add your compiler to the `PATH` environment variable, CMake might not be able to find it\. To make sure that CMake finds your toolchain file, you can use the `AFR_TOOLCHAIN_PATH` option\. This option searches the specified toolchain directory path and the toolchain's subfolder under `bin`\. For example:

```
cmake -DBOARD=cc3220 -DCMAKE_TOOLCHAIN_FILE='/path/to/toolchain_file.cmake' -DAFR_TOOLCHAIN_PATH='/path/to/toolchain/' -S . -B build-directory
```

To enable debugging, set the `CMAKE_BUILD_TYPE` to `debug`\. With this option enabled, CMake adds debug flags to the compile options, and builds FreeRTOS with debug symbols\.

```
# Build with debug symbols
cmake -DBOARD=cc3220 -DCOMPILER=arm-ti -DCMAKE_BUILD_TYPE=debug -S . -B build-directory
```

You can also set the `CMAKE_BUILD_TYPE` to `release` to add optimization flags to the compile options\.

### Generating build files \(CMake GUI\)<a name="cmake-gen-gui"></a>

You can use the CMake GUI to generate FreeRTOS build files\.

**To generate build files with the CMake GUI**

1. From the command line, issue `cmake-gui` to start the GUI\.

1. Choose **Browse Source** and specify the source input, and then choose **Browse Build** and specify the build output\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/cmake-gui1.png)

1. Choose **Configure**, and under **Specify the build generator for this project**, find and choose the build system that you want to use to build the generated build files\. if you do not see the pop up window, you might be reusing an existing build directory\. In this case, delete the CMake cache by choosing **Delete Cache** from the **File** menu\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/cmake-gui2.png)

1. Choose **Specify toolchain file for cross\-compiling**, and then choose **Next**\.

1. Choose the toolchain file \(for example, `freertos/tools/cmake/toolchains/arm-ti.cmake`\), and then choose **Finish**\.

   The default configuration for FreeRTOS is the template board, which does not provide any portable layer targets\. As a result, a window appears with the message Error in configuration process\.
**Note**  
If you are seeing the following error:  

   ```
   CMake Error at tools/cmake/toolchains/find_compiler.cmake:23 (message):
   Compiler not found, you can specify search path with AFR_TOOLCHAIN_PATH.
   ```

   It means the compiler is not in your `PATH` environment variable\. You can set the `AFR_TOOLCHAIN_PATH` variable in the GUI to tell CMake where you installed your compiler\. If you do not see the `AFR_TOOLCHAIN_PATH` variable, choose **Add Entry**\. In the pop up window, under **Name**, type **AFR\_TOOLCHAIN\_PATH**\. Under **Compiler Path** type the path to your compiler\. for example, `C:/toolchains/arm-none-eabi-gcc`\.

1. The GUI should now look like this:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/cmake-gui3.png)

   Choose **AFR\_BOARD**, choose your board, and then choose **Configure** again\.

1. Choose **Generate**\. CMake generates the build system files \(for example, makefiles or ninja files\), and these files appear in the build directory you specified in the first step\. Follow the instructions in the next section to generate the binary image\.

### Building FreeRTOS from generated build files<a name="cmake-build"></a>

#### Building with native build system<a name="gsg-cmake-native"></a>

You can build FreeRTOS with a native build system by calling the build system command from the output binaries directory\.

For example, if your build file output directory is `<build_dir>`, and you are using Make as your native build system, run the following commands:

```
cd <build_dir>
make -j4
```

#### Building with CMake<a name="gsg-cmake-build"></a>

You can also use the CMake command\-line tool to build FreeRTOS\. CMake provides an abstraction layer for calling native build systems\. For example:

```
cmake --build build_dir
```

Here are some other common uses of the CMake command\-line tool's build mode:

```
# Take advantage of CPU cores.
cmake --build build_dir --parallel 8
```

```
# Build specific targets.
cmake --build build_dir --target afr_kernel
```

```
# Clean first, then build.
cmake --build build_dir --clean-first
```

For more information about the CMake build mode, see the [CMake documentation](https://cmake.org/cmake/help/latest/manual/cmake.1.html#build-tool-mode)\.
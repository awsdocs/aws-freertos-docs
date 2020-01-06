# Building Amazon FreeRTOS with CMake<a name="building-cmake"></a>

CMake targets your host operating system as the target system by default\. To use CMake for cross compiling, you must provide a toolchain file that specifies the compiler that you want to use\. Amazon FreeRTOS provides some default toolchain files in `<amazon-freertos>/tools/cmake/toolchains`\. The instructions for using the toolchain file differ depending on whether you are using the CMake command\-line interface or the GUI\. [Generating Build Files \(CMake Command\-Line Tool\)](#cmake-gen-cli) has more details\. To learn more about cross\-compiling in CMake, visit the [ Cross Compiling](https://gitlab.kitware.com/cmake/community/wikis/doc/cmake/CrossCompiling) on the official CMake Wiki\.

**To build a CMake\-based project**

1. Run CMake to generate the build files for a native build system, like Make or Ninja\.

   You can use either the [CMake command\-line tool](https://cmake.org/cmake/help/latest/manual/cmake.1.html) or the [CMake GUI](https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html) to generate the build files for your native build system\.

   For information about generating Amazon FreeRTOS build files, see [Generating Build Files \(CMake Command\-Line Tool\)](#cmake-gen-cli) and [Generating Build Files \(CMake GUI\)](#cmake-gen-gui)\.

1. Invoke the native build system to make the project into an executable\.

   For information about making Amazon FreeRTOS build files, see [Building Amazon FreeRTOS from Generated Build Files](#cmake-build)\.

## Generating Build Files \(CMake Command\-Line Tool\)<a name="cmake-gen-cli"></a>

You can use the CMake command\-line tool \(`cmake`\) to generate build files for Amazon FreeRTOS from the command line\.

To generate the build files, you must specify the target board, compiler and the locations of your source code and build directory\. Specify the the target board with the `-DVENDOR` option\. Specify the compiler with the `-DCOMPILER` option\. Specify the location of your source code with the `-S` switch and the location of the generated build files with the `-B` switch\.

**Note**  
The compiler must be in the system's `PATH` variable, otherwise you must specify the location of the compiler\.

For example, if the vendor is Texas Instruments, and the board is the CC3220 Launchpad, and the compiler is GCC for ARM, you can issue the following command to build from the source files located in the current directory to a directory named `build`:

```
cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S . -B build
```

**Note**  
If you are using Windows, you must specify the native build system because CMake uses Visual Studio by default\. For example:  

```
cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S . -B build -G Ninja
```
Or:  

```
cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S . -B build -G "MinGW Makefiles"
```

The regular expressions `${VENDOR}.*` and `${BOARD}.*` are used to search for a matching board, so you don't have to use the full names of the vendor and board for the `VENDOR` and `BOARD` options\. Partial names work, provided there is a single match\. For example, the following commands generate the same build files from the same source:

```
cmake -DVENDOR=ti -DCOMPILER=arm-ti -S . -B build
```

```
cmake -DBOARD=cc3220 -DCOMPILER=arm-ti -S . -B build
```

```
cmake -DVENDOR=t -DBOARD=cc -DCOMPILER=arm-ti -S . -B build
```

You can use the `CMAKE_TOOLCHAIN_FILE` option if you want to use a toolchain file that is not located in the default directory `cmake/toolchains`\. For example:

```
cmake -DBOARD=cc3220 -DCMAKE_TOOLCHAIN_FILE='/path/to/toolchain_file.cmake' -S . -B build
```

If the toolchain file does not use absolute paths for your compiler, and you didn't add your compiler to the `PATH` environment variable, CMake might not be able to find it\. To make sure that CMake finds your toolchain file, you can use the `AFR_TOOLCHAIN_PATH` option\. This option searches the specified toolchain directory path and the toolchain's subfolder under `bin`\. For example:

```
cmake -DBOARD=cc3220 -DCMAKE_TOOLCHAIN_FILE='/path/to/toolchain_file.cmake' -DAFR_TOOLCHAIN_PATH='/path/to/toolchain/' -S . -B build
```

To enable debugging, set the `CMAKE_BUILD_TYPE` to `debug`\. With this option enabled, CMake adds debug flags to the compile options, and builds Amazon FreeRTOS with debug symbols\.

```
# Build with debug symbols
cmake -DBOARD=cc3220 -DCOMPILER=arm-ti -DCMAKE_BUILD_TYPE=debug -S . -B build
```

You can also set the `CMAKE_BUILD_TYPE` to `release` to add optimization flags to the compile options\.

## Generating Build Files \(CMake GUI\)<a name="cmake-gen-gui"></a>

You can use the CMake GUI to generate Amazon FreeRTOS build files\.

**To generate build files with the CMake GUI**

1. From the command line, issue `cmake-gui` to start the GUI\.

1. Choose **Browse Source** and specify the source input, and then choose **Browse Build** and specify the build output\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/cmake-gui1.png)

1. Choose **Configure**, and under **Specify the build generator for this project**, find and choose the build system that you want to use to build the generated build files\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/cmake-gui2.png)
**Note**  
If you do not see the pop up window, you might be reusing an existing build directory\. In this case, delete the CMake cache first by clicking **File\->Delete** Cache in the menu\.

1. Choose **Specify toolchain file for cross\-compiling**, and then choose **Next**\.

1. Choose the toolchain file \(for example, `<amazon-freertos>/tools/cmake/toolchains/arm-ti.cmake`\), and then choose **Finish**\.

   The default configuration for Amazon FreeRTOS is the template board, which does not provide any portable layer targets\. As a result, a window appears with the message **Error in configuration process**\.
**Note**  
If you see the following error message:  

   ```
   CMake Error at tools/cmake/toolchains/find_compiler.cmake:23 (message):
   Compiler not found, you can specify search path with "AFR_TOOLCHAIN_PATH".
   ```
It means the compiler is not in your environment variable PATH\. You can set the AFR\_TOOLCHAIN\_PATH variable in the GUI to tell CMake where you installed your compiler\. If you do not see the AFR\_TOOLCHAIN\_PATH variable, click the **Add Entry** button in the pop up window, enter AFR\_TOOLCHAIN\_PATH as the **name**, select PATH as the **type**, and enter the compiler path in the **value**, for example, "C:/toolchains/arm\-none\-eabi\-gcc"\.

1. The GUI should now look like this:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/cmake-gui3.png)

   Choose **AFR\_BOARD**, choose your board, and then choose **Configure** again\.

1. Choose **Generate**\. CMake generates the build system files \(for example, makefiles or ninja files\), and these files appear in the build directory you specified in the first step\. Follow the instructions in the next section to generate the binary image\.

## Building Amazon FreeRTOS from Generated Build Files<a name="cmake-build"></a>

You can build Amazon FreeRTOS with a native build system by calling the build system command from the output binaries directory\. For example, if your build file output directory is `build`, and you are using Make as your native build system, run the following commands:

```
cd build
make -j4
```

You can also use the CMake command\-line tool to build Amazon FreeRTOS\. CMake provides an abstraction layer for calling native build systems\. For example:

```
cmake --build <build_dir>
```

Here are some other common uses of the CMake command\-line tool's build mode:

```
# Take advantage of CPU cores.
cmake --build <build_dir> --parallel 8
```

```
# Build specific targets.
cmake --build <build_dir> --target afr_kernel
```

```
# Clean first, then build.
cmake --build <build_dir> --clean-first
```

For more information about the CMake build mode, see the [CMake documentation](https://cmake.org/cmake/help/latest/manual/cmake.1.html#build-tool-mode)\.
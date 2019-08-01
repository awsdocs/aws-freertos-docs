# Building Amazon FreeRTOS with CMake<a name="building-cmake"></a>

CMake targets your host operating system as the target system by default\. To use it for cross compiling, CMake requires a toolchain file, which specifies the compiler that you want to use\. In Amazon FreeRTOS, we provide default toolchain files in `<amazon-freertos>/tools/cmake/toolchains`\. The way to provide this file to CMake depends on whether you’re using the CMake commandline interface or GUI\. For more details, follow the [Generating Build Files \(CMake Command\-Line Tool\)](#cmake-gen-cli) instructions below\. For more information about cross\-compiling in CMake, see [CrossCompiling](https://gitlab.kitware.com/cmake/community/wikis/doc/cmake/CrossCompiling) in the official CMake wiki\.

**To build a CMake\-based project**

1. Run CMake to generate the build files for a native build system, like Make or Ninja\.

   You can use either the [CMake command\-line tool](https://cmake.org/cmake/help/latest/manual/cmake.1.html) or the [CMake GUI](https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html) to generate the build files for your native build system\.

   For information about generating Amazon FreeRTOS build files, see [Generating Build Files \(CMake Command\-Line Tool\)](#cmake-gen-cli) and [Generating Build Files \(CMake GUI\)](#cmake-gen-gui)\.

1. Invoke the native build system to make the project into an executable\.

   For information about making Amazon FreeRTOS build files, see [Building Amazon FreeRTOS from Generated Build Files](#cmake-build)\.

## Generating Build Files \(CMake Command\-Line Tool\)<a name="cmake-gen-cli"></a>

You can use the CMake command\-line tool \(cmake\) to generate build files for Amazon FreeRTOS\. To generate the build files, you need to specify a target board, a compiler and the location of the source code and build directory\. Specify the target board using the `-DVENDOR` option\. Specify the compiler with the `-DCOMPILER` option\. Use the `-S` option to specify the location of the source code\. Use the `-B`option to specify the location of generated build files\.

**Note**  
The compiler must be in the system's `PATH` variable, or you must specify the location of the compiler\.

For example, if the vendor is Texas Instruments, and the board is the CC3220 Launchpad, and the compiler is GCC for ARM, you can issue the following command to build the source files from the current directory to a directory named `build`:

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
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/cmake-gui1.png)

1. Choose **Configure**, and under **Specify the build generator for this project**, find and choose the build system that you want to use to build the generated build files\. if you do not see the pop up window, you might be reusing an existing build directory\. In this case, delete the CMake cache by choosing **Delete Cache** from the **File** menu\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/cmake-gui2.png)

1. Choose **Specify toolchain file for cross\-compiling**, and then choose **Next**\.

1. Choose the toolchain file \(for example, `amazon-freertos/tools/cmake/toolchains/arm-ti.cmake`\), and then choose **Finish**\.

   The default configuration for Amazon FreeRTOS is the template board, which does not provide any portable layer targets\. As a result, a window appears with the message Error in configuration process\.
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

1. Choose **Generate**\. After CMake generates the native build system files, the files should appear in the output binaries directory that you specified in the first step\.

## Building Amazon FreeRTOS from Generated Build Files<a name="cmake-build"></a>

### Building with Native Build System<a name="gsg-cmake-native"></a>

You can build Amazon FreeRTOS with a native build system by calling the build system command from the output binaries directory\.

For example, if your build file output directory is `<build_dir>`, and you are using Make as your native build system, run the following commands:

```
cd <build_dir>
make -j4
```

### Building with CMake<a name="gsg-cmake-build"></a>

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
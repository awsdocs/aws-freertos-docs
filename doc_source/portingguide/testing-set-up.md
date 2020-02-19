# Setting Up Your FreeRTOS Source Code for Testing<a name="testing-set-up"></a>

FreeRTOS includes tests for each ported library\. The `aws_test_runner.c` file defines a `RunTests` function that runs each test that you have specified in the `aws_test_runner_config.h` header file\. As you port each FreeRTOS library, you can test the ports by building the ported FreeRTOS source code, flashing the compiled code to your board, and running it on the board\.

To build the FreeRTOS source code for testing, you can use either of the following:
+ A supported IDE\.

  If you are using an IDE to build FreeRTOS source code, you need to set up an IDE test project\. Follow the instructions in [Creating an IDE Project](porting-create-project.md) to create a test project in your IDE\. Each library\-specific porting section under [Porting the FreeRTOS Libraries](afr-porting.md) includes additional instructions for setting up a library's ported source files in the IDE test project\.
+ The [CMake build system](https://cmake.org/)\.

  If you are using CMake, you need to create a `CMakeLists.txt` CMake list file\. Follow the instructions in [Creating a CMake List File](porting-cmake-setup.md) to create a `CMakeLists.txt` CMake list file\.
**Important**  
A `CMakeLists.txt` file is required for listing a qualified device on the FreeRTOS console, regardless of the testing method that you use\.

After you build the code, use your platform's flash utility to flash the compiled code to your device\.

**Note**  
You specify your build and flash tools in the `userdata.json` file for Device Tester, so if you are validating your ports with Device Tester, you do not need to flash your code manually\.
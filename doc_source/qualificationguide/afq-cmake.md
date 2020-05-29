# Creating a CMakeLists\.txt file for your platform<a name="afq-cmake"></a>

A `CMakeLists.txt` file is used to list your device on the FreeRTOS console, and it makes it possible for developers to build FreeRTOS code for the device without an IDE\.

**Note**  
A CMake list file is not required to qualify a board through the AWS Device Qualification Program\. The file is only required for listing devices on the FreeRTOS Console\.

For more information about the CMake build system, see [CMake\.org](https://cmake.org/overview/)\.

Follow the instructions in [Creating a list file for your platform from the CMakeLists\.txt template](cmake-template.md) to create a CMake list file from the template provided with FreeRTOS\.

**Important**  
Before you submit your CMake list file, you must verify that you can use the file to build the FreeRTOS test project and the Hello World demo project with CMake\.  
For instructions, see [Building FreeRTOS with CMake](building-cmake.md)\.
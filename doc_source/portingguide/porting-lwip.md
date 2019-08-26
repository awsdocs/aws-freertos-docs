# Porting lwIP<a name="porting-lwip"></a>

lwIP is an alternative, open source TCP/IP stack\. For more information, see [lwIP \- A Lightweight TCP/IP Stack \- Summary](https://savannah.nongnu.org/projects/lwip/)\. FreeRTOS currently supports version 2\.1\.2\.

## Prerequisites<a name="porting-prereqs-lwip"></a>

To port the lwIP stack, you need the following:
+ An IDE project or `CMakeLists.txt` list file that includes vendor\-supplied network drivers\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)\.

## Porting<a name="porting-steps-lwip"></a>

Before you port the lwIP TCP/IP stack to your device, check the `<amazon-freertos>/libraries/3rdparty/lwip/src/portable` directory to see if a port to your platform already exists\.

1. If a port does not exist, do the following:

   Under `<amazon-freertos>/libraries/3rdparty/lwip/src/portable`, create a directory named `<vendor>/<board>/netif`, where the *<vendor>* and `<board>` directories match your platform\.

1. Port the `<amazon-freertos>/libraries/3rdparty/lwip/src/netif/ethernetif.c` stub file according to the comments in the stub file\.

1. After you have created a port, or if a port already exists, in the test project's `main.c` file, add a call to `tcpip_init()`\.

1. In `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files`, create a configuration file named `lwipopts.h`\. This file must contain the following line:

   ```
   #include "arch/lwipopts_freertos.h"
   ```

   The file should also contain any platform\-specific configuration options\.

## Testing<a name="porting-testing-lwip"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

**Note**  
There are no TCP/IP porting tests specific to lwIP\.

### Setting Up the IDE Test Project<a name="testing-ide-lwip"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the lwIP source files in the IDE project**

1. Add all of the source and header files in `lwip/src` and its subdirectories to the `aws_tests` IDE project\.
**Note**  
If you added a `.c` file to the IDE project, and then edited that `.c` file for a port, you must replace the original `.c` file with the edited one in the IDE project\.

1. Add the following paths to your compiler's include path:
   + `<amazon-freertos>/libraries/3rdparty/lwip/src/include`
   + `<amazon-freertos>/libraries/3rdparty/lwip/src/portable`
   + `<amazon-freertos>/libraries/3rdparty/lwip/src/portable/<vendor>/<board>/include`

### Configuring the `CMakeLists.txt` File<a name="testing-cmake-lwip"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [Amazon FreeRTOS Portable Layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `<amazon-freertos>/vendors/<vendor>/boards/<board>/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.
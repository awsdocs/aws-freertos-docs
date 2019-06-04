# Porting lwIP<a name="porting-lwip"></a>

lwIP is an alternative, open source TCP/IP stack\. For more information, see [lwIP \- A Lightweight TCP/IP Stack \- Summary](https://savannah.nongnu.org/projects/lwip/)\. FreeRTOS currently supports version 2\.0\.3\.

## Prerequisites<a name="porting-prereqs-lwip"></a>

To port the lwIP stack, you need the following:
+ An IDE project that includes vendor\-supplied Ethernet drivers\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)\.

## Porting<a name="porting-steps-lwip"></a>

Before you port the lwIP TCP/IP stack to your device, check the `<amazon-freertos>/lib/third_party/lwip/src/portable` directory to see if a port to your platform already exists\.

1. If a port does not exist, do the following:

   Under `<amazon-freertos>/lib/third_party/lwip/src/portable`, create a directory named `<vendor>/<board>/netif`, where the *<vendor>* and `<board>` directories match your platform\.

1. Add the `<amazon-freertos>/lib/third_pary/lwip/src/netif/ethernetif.c` stub file to `aws_tests/lib/third_party/lwip/src/portable/<vendor>/<board>/netif`, and port the file according to the comments in the stub file\.

1. After you have created a port, or if a port already exists, in the test project's `main.c` file, add a call to `tcpip_init()`\.

1. In `<amazon-freertos>/tests/<vendor>/<board>/common/config_files`, create a configuration file named `lwipopts.h`\. This file must contain the following line:

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

1. In your IDE, under `aws_tests/lib/third_party`, create a virtual folder named `lwip`, and then under `aws_tests/lib/third_party/lwip`, create a virtual folder named `src`\.

1. Under `aws_tests/lib/third_party/lwip/src`, create a virtual folder named `portable`, and then under `aws_tests/lib/third_party/lwip/src/portable`, create a virtual folder named `arch`\.

1. Add the source files in `<amazon-freertos>/lib/third_party/lwip/src` to the virtual folder `aws_tests/lib/third_party/lwip/src`\.

1. Add the source files in `<amazon-freertos>/lib/third_party/lwip/src/portable/arch` to the virtual folder `aws_tests/lib/third_party/lwip/src/portable/arch`\.

1. Add the files and directories from `<amazon-freertos>/lib/third_party/lwip/src/portable/<vendor>/<board>` to the `aws_tests/lib/third_party/lwip/src/portable/<vendor>/<board>` virtual folder\.
**Note**  
If you added a `.c` file to `aws_tests/lib/third_party/lwip/src`, and then edited that `.c` file for a port, you must replace the original `.c` file with the edited one in the `aws_tests/lib/third_party/lwip/src` virtual folder\.

1. Add the following paths to your compiler's include path:
   + `<amazon-freertos>/lib/third_party/lwip/src/include`
   + `<amazon-freertos>/lib/third_party/lwip/src/portable`
   + `<amazon-freertos>/lib/third_party/lwip/src/portable/<vendor>/<board>/include`
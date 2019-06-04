# Porting FreeRTOS\+TCP<a name="porting-freertos-tcp"></a>

FreeRTOS\+TCP is a native TCP/IP stack for the FreeRTOS kernel\. For more information, see [FreeRTOS\.org](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/)\.

## Prerequisites<a name="porting-prereqs-freertos-tcp"></a>

To port the FreeRTOS\+TCP library, you need the following:
+ An IDE project that includes the vendor\-supplied Ethernet drivers\.

  For information about setting up a test project, see [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)\.

## Porting<a name="porting-steps-freertos-tcp"></a>

Before you start porting the FreeRTOS\-TCP library, check the `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source/portable/NetworkInterface` directory to see if a port to your device already exists\.

If a port does not exist, do the following:

1. Rename the `<board_family>` directory under `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source/portable/NetworkInterface` to your device's MCU family name\.

1. Follow the [Porting FreeRTOS\+TCP to a Different Microcontroller](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/Embedded_Ethernet_Porting.html) instructions on FreeRTOS\.org to port FreeRTOS\+TCP to your device\.

1. If necessary, follow the [Porting FreeRTOS\+TCP to a New Embedded C Compiler](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/Embedded_Compiler_Porting.html) instructions on FreeRTOS\.org to port FreeRTOS\+TCP to a new compiler\.

1. Implement a new port that uses the vendor\-supplied Ethernet drivers in a file called `NetworkInterface.c`, and save the file to `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source/portable/NetworkInterface/<board_family>`, the directory that you renamed in the first step\.
**Note**  
The files in the `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source/portable/BufferManagement` directory are used by multiple ports\. Do not edit the files in this directory\.

After you create a port, or if a port already exists, open `<amazon-freertos>/tests/<vendor>/<board>/common/config_files/FreeRTOSIPConfig.h`, and edit the configuration options so they are correct for your platform\. For more information about the configuration options, see [FreeRTOS\+TCP Configuration](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/TCP_IP_Configuration.html) on FreeRTOS\.org\.

## Testing<a name="porting-testing-freertos-tcp"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting Up the IDE Test Project<a name="testing-ide-freertos-tcp"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the FreeRTOS\+TCP library in the IDE project**

1. In your IDE, under `aws_tests/lib`, create a virtual folder named `FreeRTOS-Plus-TCP`\.

1. Under `aws_tests/lib/FreeRTOS-Plus-TCP`, create virtual folders named `sources`, `include`, and `portable`\.

1. Add the source files in `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source` to the virtual folder `aws_tests/lib/FreeRTOS-Plus-TCP/source`\.

1. Add the header files in `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/include` to the virtual folder `aws_tests/lib/FreeRTOS-Plus-TCP/include`\.

1. Under `aws_tests/lib/FreeRTOS-Plus-TCP/portable`, create a virtual folder named `NetworkInterface`\.

1. Add the port source files in `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source/portable/NetworkInterface/<board_family>` to the virtual folder `aws_tests/lib/FreeRTOS-Plus-TCP/portable/NetworkInterface`\.

1. Under `aws_tests/lib/FreeRTOS-Plus-TCP/portable`, create a virtual folder named `BufferManagement`\.

1. Add `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/source/portable/BufferManagement/BufferAllocation_2.c` to the virtual folder `aws_tests/lib/FreeRTOS-Plus-TCP/portable/BufferManagement`\.
**Note**  
FreeRTOS includes five example heap management implementations under `<amazon-freertos>/lib/FreeRTOS/portable/MemMang`\. FreeRTOS\+TCP and `BufferAllocation_2.c` require the `heap_4.c` or `heap_5.c` implementations\. You must use `heap_4.c` or `heap_5.c` to ensure that the Amazon FreeRTOS demo applications run properly\. Do not use a custom heap implementation\.

1. Add `<amazon-freertos>/lib/FreeRTOS-Plus-TCP/include` to your compiler's include path\.

### Setting Up Your Local Testing Environment<a name="testing-local-freertos-tcp"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the TCP tests**

1. Open `<amazon-freertos>/lib/utils/aws_system_init.c`, and in the function `SYSTEM_Init()`, comment out the lines that call `BUFFERPOOL_Init()` and `MQTT_AGENT_Init()`, if you have not done so already\. Bufferpool and the MQTT agent are not used in this library's porting tests\. When you reach the [Setting Up the MQTT Library for Testing](afr-porting-mqtt.md) section, you will be instructed to uncomment these initialization function calls for testing the MQTT library\.

   If you have not ported the Secure Sockets library, also comment out the line that calls `SOCKETS_Init()`\. When you reach the [Porting the Secure Sockets Library](afr-porting-ss.md) section, you will be instructed to uncomment this initialization function call\.

1. Open `<amazon-freertos>/tests/<vendor>/<board>/common/application_code/main.c`, and uncomment the call to `FreeRTOS_IPInit()`\.

1. Fill the following arrays with valid values for your network:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-freertos-tcp.html)

1. Open `<amazon-freertos>/tests/vendor/board/common/config_files/FreeRTOSIPConfig.h`, and set the `ipconfigUSE_NETWORK_EVENT_HOOK` macro to `1`\.

1. Open `<amazon-freertos>/tests/<vendor>/<board>/common/application_code/main.c`, and add the following code to the beginning of the function definition for `vApplicationIPNetworkEventHook()`:

   ```
   if (eNetworkEvent == eNetworkUp)
   {
       configPRINT("Network connection successful. \n\r");
   }
   ```

### Running the Tests<a name="testing-run-freertos-tcp"></a>

**To execute the FreeRTOS\+TCP tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\. If `Network connection successful` appears, the Ethernet driver successfully connected to the network, and the test is complete\.

## Validation<a name="w3aac11c21c11c11"></a>

To officially qualify a device for Amazon FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.
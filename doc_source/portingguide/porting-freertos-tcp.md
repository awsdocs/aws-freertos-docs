# Porting FreeRTOS\+TCP<a name="porting-freertos-tcp"></a>

FreeRTOS\+TCP is a native TCP/IP stack for the FreeRTOS kernel\. For more information, see [FreeRTOS\.org](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/)\.

## Prerequisites<a name="porting-prereqs-freertos-tcp"></a>

To port the FreeRTOS\+TCP library, you need the following:
+ An IDE project or `CMakeLists.txt` list file that includes the vendor\-supplied Ethernet or Wi\-Fi drivers\.

  For information about setting up a test project, see [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)\.

## Porting<a name="porting-steps-freertos-tcp"></a>

Before you start porting the FreeRTOS\-TCP library, check the `<freertos>/libraries/freertos_plus/standard/freertos_plus_tcp/source/portable/NetworkInterface` directory to see if a port to your device already exists\.

If a port does not exist, do the following:

1. Follow the [Porting FreeRTOS\+TCP to a Different Microcontroller](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/Embedded_Ethernet_Porting.html) instructions on FreeRTOS\.org to port FreeRTOS\+TCP to your device\.

1. If necessary, follow the [Porting FreeRTOS\+TCP to a New Embedded C Compiler](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/Embedded_Compiler_Porting.html) instructions on FreeRTOS\.org to port FreeRTOS\+TCP to a new compiler\.

1. Implement a new port that uses the vendor\-supplied Ethernet or Wi\-Fi drivers in a file called `NetworkInterface.c`, and save the file to `<freertos>/libraries/freertos_plus/standard/freertos_plus_tcp/source/portable/NetworkInterface/<board_family>`\.
**Note**  
The files in the `<freertos>/libraries/freertos_plus/standard/freertos_plus_tcp/source/portable/BufferManagement` directory are used by multiple ports\. Do not edit the files in this directory\.

After you create a port, or if a port already exists, open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/FreeRTOSIPConfig.h`, and edit the configuration options so they are correct for your platform\. For more information about the configuration options, see [FreeRTOS\+TCP Configuration](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/TCP_IP_Configuration.html) on FreeRTOS\.org\.

## Testing<a name="porting-testing-freertos-tcp"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting Up the IDE Test Project<a name="testing-ide-freertos-tcp"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the FreeRTOS\+TCP library in the IDE project**

1. Add all of the source and header files in `<freertos>/libraries/freertos_plus/standard/freertos_plus_tcp` and its subdirectories to the `aws_tests` IDE project\.
**Note**  
FreeRTOS includes five example heap management implementations under `<freertos>/freertos_kernel/portable/MemMang`\. FreeRTOS\+TCP and `BufferAllocation_2.c` require the `heap_4.c` or `heap_5.c` implementations\. You must use `heap_4.c` or `heap_5.c` to ensure that the FreeRTOS demo applications run properly\. Do not use a custom heap implementation\.

1. Add `<freertos>/libraries/freertos_plus/standard/freertos_plus_tcp/include` to your compiler's include path\.

### Configuring the `CMakeLists.txt` File<a name="testing-cmake-freertos-tcp"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS Portable Layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `<freertos>/vendors/<vendor>/boards/<board>/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

### Setting Up Your Local Testing Environment<a name="testing-local-freertos-tcp"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the TCP tests**

1. Open `<freertos>/libraries/freertos_plus/standard/utils/src/iot_system_init.c`, and in the function `SYSTEM_Init()`, comment out the lines that call `BUFFERPOOL_Init()` and `MQTT_AGENT_Init()`, if you have not done so already\. Bufferpool and the MQTT agent are not used in this library's porting tests\. When you reach the [Configuring the MQTT Library for Testing](afr-porting-mqtt.md) section, you will be instructed to uncomment these initialization function calls for testing the MQTT library\.

   If you have not ported the Secure Sockets library, also comment out the line that calls `SOCKETS_Init()`\. When you reach the [Porting the Secure Sockets Library](afr-porting-ss.md) section, you will be instructed to uncomment this initialization function call\.

1. Open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/application_code/main.c`, and uncomment the call to `FreeRTOS_IPInit()`\.

1. Fill the following arrays with valid values for your network:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-freertos-tcp.html)

1. Open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/FreeRTOSIPConfig.h`, and set the `ipconfigUSE_NETWORK_EVENT_HOOK` macro to `1`\.

1. Open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/application_code/main.c`, and add the following code to the beginning of the function definition for `vApplicationIPNetworkEventHook()`:

   ```
   if (eNetworkEvent == eNetworkUp)
   {
       configPRINT("Network connection successful. \n\r");
   }
   ```

### Running the Tests<a name="testing-run-freertos-tcp"></a>

**To execute the FreeRTOS\+TCP tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\. If `Network connection successful` appears, the Ethernet or Wi\-Fi driver successfully connected to the network, and the test is complete\.

## Validation<a name="w3aac11c25c11c11"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [ Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.
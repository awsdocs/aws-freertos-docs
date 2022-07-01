# Porting a TCP/IP stack<a name="afr-porting-tcp"></a>

This section provides instruction for porting and testing on\-board TCP/IP stacks\. If your platform offloads TCP/IP and TLS functionality to a separate network processor or module, you can skip this porting section and visit [Porting the Network Transport Interface](afr-porting-network-transport-interface.md)\.

[FreeRTOS\+TCP](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html) is a native TCP/IP stack for the FreeRTOS kernel\. FreeRTOS\+TCP is developed and maintained by the FreeRTOS engineering team and is the recommended TCP/IP stack to use with FreeRTOS\. For more information, see [Porting FreeRTOS\+TCP](#porting-freertos-tcp)\. Alternatively, you can use the third\-party TCP/IP stack [lwIP](https://savannah.nongnu.org/projects/lwip/) \. The testing instruction provided in this section uses the transport interface tests for TCP plain text, and is not dependent on the specific implemented TCP/IP stack\. 

## Porting FreeRTOS\+TCP<a name="porting-freertos-tcp"></a>

FreeRTOS\+TCP is a native TCP/IP stack for the FreeRTOS kernel\. For more information, see [FreeRTOS\.org](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/)\.

### Prerequisites<a name="porting-prereqs-freertos-tcp"></a>

To port the FreeRTOS\+TCP library, you need the following:
+ An IDE project that includes the vendor\-supplied Ethernet or Wi\-Fi drivers\.

  For information about setting up a test project, see [Setting up your workspace and project for porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)\.

### Porting<a name="porting-steps-freertos-tcp"></a>

Before you start porting the FreeRTOS\+TCP library, check the [GitHub](https://github.com/FreeRTOS/FreeRTOS-Plus-TCP/tree/main/portable/NetworkInterface) directory to see if a port to your board already exists\.

If a port does not exist, do the following:

1. Follow the [Porting FreeRTOS\+TCP to a Different Microcontroller](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/Embedded_Ethernet_Porting.html) instructions on FreeRTOS\.org to port FreeRTOS\+TCP to your device\.

1. If necessary, follow the [Porting FreeRTOS\+TCP to a New Embedded C Compiler](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/Embedded_Compiler_Porting.html) instructions on FreeRTOS\.org to port FreeRTOS\+TCP to a new compiler\.

1. Implement a new port that uses the vendor\-supplied Ethernet or Wi\-Fi drivers in a file called `NetworkInterface.c`\. Visit the [GitHub](https://github.com/FreeRTOS/FreeRTOS-Plus-TCP/tree/main/portable/NetworkInterface/board_family) repository for a template\.

After you create a port, or if a port already exists, create `FreeRTOSIPConfig.h`, and edit the configuration options so they are correct for your platform\. For more information about the configuration options, see [FreeRTOS\+TCP Configuration](https://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/TCP_IP_Configuration.html) on FreeRTOS\.org\.

## Testing<a name="testing-tcp"></a>

Whether you use FreeRTOS\+TCP library or a third party library, follow the steps below for testing:
+ Provide an implementation for `connect/disconnect/send/receive` APIs in transport interface tests\.
+ Setup an echo server in plain text TCP connection mode, and run transport interface tests\.

**Note**  
To officially qualify a device for FreeRTOS, if your architecture requires to port a TCP/IP software stack, you need to validate the device's ported source code against transport interface tests in plain text TCP connection mode with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *FreeRTOS User Guide* to set up AWS IoT Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.
# What Is Amazon FreeRTOS?<a name="what-is-amazon-freertos"></a>

Amazon FreeRTOS consists of the following components:
+ A microcontroller operating system based on the FreeRTOS kernel
+ Amazon FreeRTOS libraries for connectivity, security, and over\-the\-air \(OTA\) updates\.
+ A console that allows you to download a zip file that contains everything you need to get started with Amazon FreeRTOS\.
+ Over\-the\-air \(OTA\) Updates\.
+ The Amazon FreeRTOS Qualification Program\.

## The FreeRTOS Kernel<a name="freertos-kernel"></a>

The FreeRTOS kernel is a real\-time operating system kernel that supports numerous architectures and is ideal for building embedded microcontroller applications\. The kernel provides:
+ A multitasking scheduler\. 
+ Multiple memory allocation options \(including the ability to create statically allocated systems\)\.
+ Inter\-task coordination primitives, including task notifications, message queues, multiple types of semaphores, and stream and message buffers\.

## Amazon FreeRTOS Libraries<a name="freertos-libraries"></a>

Amazon FreeRTOS includes libraries that enable you to:
+ Securely connect devices to the AWS IoT cloud using MQTT and device shadows\.
+ Discover and connect to AWS IoT Greengrass cores\.
+ Manage Wi\-Fi connections\.
+ Audit the configuration of your devices, monitor connected devices to detect abnormal behavior, and to mitigate security risks\. For more information, see [AWS IoT Device Defender](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender.html)\. Amazon FreeRTOS provides a library that enables your Amazon FreeRTOS\-based devices to write metrics to AWS IoT Device Defender\. For more information, see [Amazon FreeRTOS Device Defender Library](https://docs.aws.amazon.com/freertos/latest/userguide/afr-device-defender-library.html)\.
**Note**  
The Device Defender library currently works on the Microchip Curiosity PIC32MZEF development board and the Windows simulator\.
+ Listen for and process over\-the\-air \(OTA\) updates\.

## Amazon FreeRTOS Console<a name="freertos-ocw"></a>

The [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) enables you to configure and download a package that contains everything you need to write an application for your microcontroller\-based devices:
+ The FreeRTOS kernel
+ Amazon FreeRTOS libraries
+ Platform support libraries
+ Hardware drivers

You can download a package with a predefined configuration or create your own configuration by selecting your hardware platform and the libraries required for your application\. These configurations are saved in AWS and are available for download at any time\.

The Amazon FreeRTOS console is part of the AWS IoT console\. You can find it by choosing the link above or by browsing to the AWS IoT console\.

**To open the Amazon FreeRTOS console**

1. Browse to the AWS IoT console\.

1. From the navigation pane choose **Software**\.

1. Under **Amazon FreeRTOS Device Software** choose **Configure Download**\.

## Downloading Amazon FreeRTOS Source Code<a name="freertos-mds-projects-github"></a>

You can download the RTOS kernel and software libraries from the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) or from [GitHub](https://github.com/aws/amazon-freertos)\.

## Over\-the\-Air Updates<a name="freertos-ota-updates"></a>

Internet\-connected devices can be in use for a long time, and must be updated periodically to fix bugs and improve functionality\. Often these devices must be updated in the field and need to be updated remotely or "over\-the\-air"\. The Amazon FreeRTOS Over\-the\-Air \(OTA\) Update service enables you to:
+ Digitally sign firmware prior to deployment\.
+ Securely deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, reset, or reprovisioned\.
+ Once deployed to devices, verify the authenticity and integrity of the new firmware\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.

When you send files over the air, it is a best practice to digitally sign them so that the devices that receive the files can verify they have not been tampered with en route\. You can use Code Signing for Amazon FreeRTOS to sign and encrypt your files or you can sign your files with your own code\-signing tools\. For more information about Code Signing for Amazon FreeRTOS, see the [Code Signing for Amazon FreeRTOS Developer Guide](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.

For more information about OTA updates, see:
+ [Amazon FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)
+ [OTA Demo Application](ota-demo.md)
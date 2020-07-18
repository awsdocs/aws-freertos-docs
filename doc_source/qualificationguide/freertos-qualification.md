# Qualifying Your Device<a name="freertos-qualification"></a>

**To qualify your device for FreeRTOS**

1. Port the FreeRTOS libraries to your device\.
**Note**  
Currently, ports of the FreeRTOS OTA and Bluetooth Low Energy libraries are not required for qualification\.  
If your device does not support Wi\-Fi, you can use an ethernet connection to connect to the AWS Cloud instead\. A port of the FreeRTOS Wi\-Fi library is not necessarily required\.

   For instructions on porting FreeRTOS to your device, see the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.

1. Validate your ports with AWS IoT Device Tester for FreeRTOS\.

   When using Device Tester to validate your ports for qualification, you must specify information in the `features` attribute of the `device.json` configuration file about the following ports:
   + TCP/IP

     ```
     {
         "name": "TCP/IP",
         "value": "On-chip | Offloaded | No"
     }
     ```
   + TLS

     ```
     {
         "name": "TLS",
         "value": "On-chip | Offloaded | No"
     }
     ```
   + Wi\-Fi

     ```
     {
         "name": "WIFI",
         "value": "Yes | No"
     }
     ```
   + OTA

     ```
     {
         "name": "OTA",
         "value": "Yes | No"
     }
     ```

   Device Tester uses this information to determine which tests to run against your ported FreeRTOS code\. Device Tester runs all other required library port tests by default\.

   For information about AWS IoT Device Tester for FreeRTOS, see [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide\.

1. Create the following for qualification submission:
   + A "Hello World" demo application that publishes messages from your device to the AWS Cloud over MQTT\.

     For information, see [Setting up a hello world demo](afq-hw-demo.md)\.
   + A "Getting Started with FreeRTOS" guide for your device\.

     For information, see [Creating a getting started with FreeRTOS guide for your device](afq-gsg.md)\.
   + A `CMakeLists.txt` file for building FreeRTOS applications for your device\.
**Note**  
A CMake list file is not required to qualify a board through the AWS Device Qualification Program\. The file is only required for listing devices on the FreeRTOS Console\. The file is also required to build project files for your platform using CMake\. 

     For information, see [Creating a CMakeLists\.txt file for your platform](afq-cmake.md)\.
   + A list of detailed information for your hardware platform\.

     For information, see [FreeRTOS Qualification Checklist](afq-checklist.md)\.
   + An appropriate open source license file for your device's FreeRTOS port\.

     For information, see [Providing an open source license for your code](afq-license.md)\.
   + \(For boards qualifying for OTA updates\) Instructions for code\-signing\.

     For examples, see [ Create a Code\-Signing Certificate](https://docs.aws.amazon.com/freertos/latest/userguide/ota-code-sign-cert.html) in the FreeRTOS User Guide\.
   + \(For boards qualifying for OTA updates that use custom bootloader\) Information and instructions on the custom bootloader application\.

     For a list of requirements, see [Porting the Bootloader Demo](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-bootloader.html.html) in the FreeRTOS Porting Guide\.

   These items are required for your device to be listed on the FreeRTOS console, for your device's code to be on GitHub, and for your device to receive Getting Started documentation support\.

1. Verify that you have all that you need to submit your board for qualification with the FreeRTOS qualification check script\.

   For more information about running the qualification check script, see [FreeRTOS qualification check script](afq-script.md)\.

1. Submit your qualified board for listing in the AWS Partner Device Catalog through the [Device Listing Portal](https://partnercentral.awspartner.com/DeviceListPage) on APN Partner Central\. All submissions require an AWS IoT Device Tester test result file that indicates that you passed all mandatory test cases\. You must be a registered APN Partner to submit your board for listing\.

You can use the [FreeRTOS Qualification Checklist](afq-checklist.md) to keep track of the list of required steps for qualification\.
# Qualifying your board<a name="freertos-qualification"></a>

## Prerequisites<a name="prerequistes-qualification"></a>

Hardware requirements:

The MCU\-based development board on which the FreeRTOS AWS IoT libraries run must have:
+ Ethernet, Wi\-Fi, or cellular connectivity capability

Software requirements:

The [Porting flowchart](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-chart.html) in the *FreeRTOS Porting Guide* identifies the required FreeRTOS AWS IoT libraries for any given MCU\-based development board\. The minimum subset is:
+ FreeRTOS kernel
+ coreMQTT
+ AWS IoT Over\-The\-Air update \(OTA\)

Testing requirements:
+ Verify the implementation of hardware platform specific APIs required by FreeRTOS libraries against the defined [tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests) GitHub repository using AWS IoT Device Tester for FreeRTOS\. See [Verify the FreeRTOS libraries ported using AWS IoT Device Tester \(IDT\)](#qualifying-your-device-idt)\.
+ Verify the interoperability with AWS IoT Core using Device Advisor\. See [](#qualifying-your-device-da)\.

## Qualification steps<a name="qualifying-your-device"></a>

### Verify the FreeRTOS libraries ported using AWS IoT Device Tester \(IDT\)<a name="qualifying-your-device-idt"></a>

1. Port the FreeRTOS libraries to your board\. See the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/) for instructions\.

1. Create a test project, and port the required tests from [FreeRTOS\-Libraries\-Integration\-Tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests) GitHub repository\. Call the test runner task [ RunQualificationTest](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests/blob/a8cafefc714cdbb4ee1d0f97c92e9c4cc75059fd/src/qualification_test.c#L52)\. 
**Note**  
For a good developer experience, it is recommended to port the FreeRTOS libraries, and run corresponding individual test group locally using an IDE to verify the integration\.  
The test runner task runs in an individual test project, or in your demo application project\.

1. Create a `manifest.yml` file to list all dependencies used in your qualifications\. The dependencies include the FreeRTOS libraries, and test repositories\. See [FreeRTOS manifest file instructions](afq-checklist-manifest-instr.md) for details\. 
**Note**  
The `manifest.yml` is used by IDT to find the required dependencies for integrity checks against specific FreeRTOS library versions, and to configure test project to build, flash and run the test binaries\.   
IDT does not mandate a specific project structure, and uses the reference path included in the `manifest.yml` file\.

1. <a name="qualifying-your-device-da"></a>Verify AWS IoT interoperability using Device Advisor\.

   1. Create a demo project that uses the same components including FreeRTOS libraries, porting, integration tasks like OTA used in the above testing\.

      For qualification, the demo application must provide the following features:
      + Perform MQTT publish and subscribe to a topic\.
      + Perform OTA updates\.
      + Create a bootloader that supports OTA updates\. Use your own bootloader or [MCUBoot](https://freertos.org/mcuboot/) \. See [Labs\-FreeRTOS\-Plus\-MCUBoot](https://github.com/FreeRTOS/Lab-Project-FreeRTOS-MCUBoot)\.
**Note**  
The [FreeRTOS GitHub](https://github.com/FreeRTOS/FreeRTOS) repository has pre\-configured examples demonstrating individual tasks\. There is also an integrated [coreMQTT Agent Demo](https://github.com/FreeRTOS/coreMQTT-Agent-Demos) that incorporates both coreMQTT and OTA tasks\. Also, see FreeRTOS Featured IoT Integrations at [Examples of qualification projects](examples-qualification.md)\. 

   1. AWS IoT Device Tester will run your demo against [AWS IoT Device Advisor](https://docs.aws.amazon.com/iot/latest/developerguide/device-advisor.html)\. The following Device Advisor test cases are required for qualification\.  
**Test cases**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/freertos-qualification.html)

1. Run the tests from AWS IoT Device Tester and generate a test report\.
   + IDT configure tests, and does a build and flash to your board automatically\. To enable this, you must configure IDT to run the build and flash commands for your device in the `userdata.json` file\. See [ Configure build, flash, and test settings](https://docs.aws.amazon.com/freertos/latest/userguide/lts-qual-steps.html#lts-cfg-dt-ud) in the [IDT for FreeRTOS User Guide](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html)\.
   + Provide device supported features in `device.json` file such as connectivity type, cryptography algorithm, key provisioning method for IDT to determine applicable tests to run\. See [ Create a device pool in IDT for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/lts-qual-steps.html#lts-cfg-dt-dp) in the [IDT for FreeRTOS User Guide](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html)\. 
   + Create and configure your AWS account for IDT to create the required cloud resources\. See [ Create and configure AWS account for IDT to create required cloud resources](https://docs.aws.amazon.com/freertos/latest/userguide/lts-qual-steps.html#lts-cfg-aws-afr) in the [IDT for FreeRTOS User Guide](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html)\. 

### Prepare for submission<a name="qualifying-your-device-preparation"></a>

1. Write a **Getting Started Guide** to run the MQTT or OTA demo project on your device\. See [ Creating a getting started with FreeRTOS guide for your device](https://docs.aws.amazon.com/freertos/latest/qualificationguide/afq-gsg.html) for instructions\.

1. Provide threat modeling document verifying that you mitigate the risks defined in the [ Threat Modeling for the AWS IoT device bootloader](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html#afr-bootloader) described in [Porting the OTA library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html) in the *FreeRTOS Porting Guide*\. This document must be uploaded as a Supporting Asset when submitting your device in [APN Partner Central](https://partnercentral.awspartner.com)\. 

1. Provide a public repository for code downloads\. We recommend that you provide a corporate GitHub repository link\.

### Qualification submission<a name="qualifying-your-device-submission"></a>
+ IDT test report\.
+ AWS IoT Device Advisor test report\.
+ Threat modeling document\.
+ GitHub repository with the source code for downloads\.
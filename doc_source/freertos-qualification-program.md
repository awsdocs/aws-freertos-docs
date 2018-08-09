# Amazon FreeRTOS Qualification Program<a name="freertos-qualification-program"></a>

This section provides information for MCU vendors about the Amazon FreeRTOS qualification workflow, which includes:
+ Creating an Amazon FreeRTOS project\.
+ Porting Amazon FreeRTOS abstraction layers\.
+ Running tests\.
+ Submitting test results to the Amazon FreeRTOS team for final qualification\.

## What's in it for OEMs?<a name="freertos-qualification-oem"></a>

The Amazon FreeRTOS Qualification Program intends to give confidence to OEM/ODM developers that by using a qualified microcontroller \(MCU\) from this program for their IoT device, they can run Amazon FreeRTOS on the device without compatibility issues\. It works with AWS IoT or AWS Greengrass\. This allows OEM/ODM developers to focus on the code for their device functionality\.

## Qualification Program for MCU Vendors<a name="freertos-qualification-vendors"></a>

The Amazon FreeRTOS Qualification Program gives MCU vendors confidence that their qualified MCUs are secure and interoperate with AWS IoT and AWS Greengrass\. This means that the MCUs and associated libraries meet the security, functionality, and performance requirements to work seamlessly with AWS IoT and AWS Greengrass\. A qualified MCU is included in the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), where customers can select it and download its libraries\. These include Amazon FreeRTOS and board support packages \(BSPs\) and drivers\. Details of the qualified MCU, along with relevant links and the MCU vendor company logo, are available on the Amazon FreeRTOS Partners web page\. The rest of this FAQ describes the steps to qualify your MCU and verify that your software \(including drivers and BSPs\) functionally integrates with Amazon FreeRTOS software\.

## Contact Amazon<a name="freertos-qualification-contact"></a>

If you want to qualify an MCU, send a request to freertos\-qual@amazon\.com\. A representative from the qualification support team will contact you and support you through the qualification steps\.

## Sign Up for the AWS Partner Network<a name="freertos-partner-net"></a>

The AWS Partner Network \(APN\) is the global partner program for AWS\. It is focused on helping APN Partners build successful AWS\-based businesses or solutions by providing business, technical, marketing, and go\-to\-market support\. To find and register for the APN Partner program, see [AWS Partner Network](https://partnercentral.awspartner.com/AWS_Partner_Application)\.

## Jointly Agree on Terms and Conditions<a name="freertos-partner-collab"></a>

After you become an APN Partner, you and AWS jointly review and agree on general terms and conditions, schedules, and initiatives in the partnership\. The agreement includes topics such as the purpose of collaboration, alliance team, initiative development, marketing and collateral, indemnification, limitations of liability, and other general terms\. After you and AWS sign the agreement, you can start the qualification workflow process\.

## Pass Qualification Test Suite<a name="freertos-partner-workflow"></a>

There are several steps to verify that your software \(including drivers and BSPs\) is functionally integrated with Amazon FreeRTOS software\. The goal of this process is to create an MCU development project that successfully builds and runs a range of functional, performance, and security tests on your MCU\.

The high\-level steps are:

1. Download the latest version of the Amazon FreeRTOS source code\.

1. Create a project using the target IDE and compiler that demonstrates the equivalent of a “Hello World” example for the target MCU\.

1. Add Amazon FreeRTOS files and resources to the created project, and confirm the project still builds\. At this stage, the included TQP tests should build and run, but are expected to fail because they have not yet been ported to your hardware\.

1. Enable each Amazon FreeRTOS feature to work successfully on your MCU\. This involves implementing each hardware\-dependent layer of the Amazon FreeRTOS feature abstractions\. Test these implementations and fix issues\.

1. When all included tests pass, submit your results \(test reports\) and your microcontroller hardware to Amazon to confirm the qualification process\.

## Amazon FreeRTOS Qualified<a name="freertos-partner-qualification"></a>

After your hardware passes the verification tests, it is considered Amazon FreeRTOS\-qualified\. Information about your hardware will be displayed in the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) and the Amazon FreeRTOS Getting Started page\.
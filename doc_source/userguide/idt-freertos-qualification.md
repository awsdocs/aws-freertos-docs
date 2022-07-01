# Use IDT with FreeRTOS qualification suite 1\.0 \(FRQ 1\.0\)<a name="idt-freertos-qualification"></a>

We do not recommend new devices to qualify using FRQ 1\.0\. Instead use [Use IDT with FreeRTOS qualification suite 2\.0 \(FRQ 2\.0\)](lts-idt-freertos-qualification.md) 

 You can use IDT for FreeRTOS qualification to verify that the FreeRTOS operating system works locally on your device and can communicate with AWS IoT\. Specifically, it verifies that the porting layer interfaces for the FreeRTOS libraries are implemented correctly\. It also performs end\-to\-end tests with AWS IoT Core\. For example, it verifies your board can send and receive MQTT messages and process them correctly\. The tests run by IDT for FreeRTOS are defined in the [FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

The tests run as embedded applications that are flashed onto your board\. The application binary images include FreeRTOS, the semiconductor vendor’s ported FreeRTOS interfaces, and board device drivers\. The purpose of the tests is to verify the ported FreeRTOS interfaces function correctly on top of the device drivers\. 

IDT for FreeRTOS generates test reports that you can submit to AWS IoT to add your hardware to the AWS Partner Device Catalog\. For more information, see [AWS Device Qualification Program](https://aws.amazon.com/partners/dqp/)\.

IDT for FreeRTOS runs on a host computer \(Windows, macOS, or Linux\) that is connected to the board to be tested\. IDT executes test cases and aggregates results\. It also provides a command line interface to manage test execution\.

In addition to testing devices, IDT for FreeRTOS creates resources \(for example, AWS IoT things, FreeRTOS groups, Lambda functions, and so on\) to facilitate the qualification process\. To create these resources, IDT for FreeRTOS uses the AWS credentials configured in the `config.json` to make API calls on your behalf\. These resources are provisioned at various times during a test\.

When you run IDT for FreeRTOS on your host computer, it performs the following steps:

1. Loads and validates your device and credentials configuration\.

1. Performs selected tests with the required local and cloud resources\.

1. Cleans up local and cloud resources\.

1. Generates tests reports that indicate if your board passed the tests required for qualification\.

**Topics**
+ [Prerequisites](dev-tester-prereqs.md)
+ [Preparing to test your microcontroller board for the first time](qual-steps.md)
+ [Use the IDT for FreeRTOS user interface to run the FreeRTOS qualification suite](device-tester-ui.md)
+ [Running Bluetooth Low Energy tests](afr-bridgekeeper-dt-bt.md)
+ [Running the FreeRTOS qualification suite](run-tests.md)
+ [Understanding results and logs](results-logs.md)
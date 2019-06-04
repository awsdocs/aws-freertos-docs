# Using AWS IoT Device Tester for Amazon FreeRTOS<a name="device-tester-for-freertos-ug"></a>

You can use AWS IoT Device Tester \(IDT\) for Amazon FreeRTOS to verify that the Amazon FreeRTOS operating system works locally on your device and can communicate with the AWS IoT Cloud\. Specifically, it verifies that the porting layer interfaces for the Amazon FreeRTOS libraries are implemented correctly\. It also performs end\-to\-end tests with AWS IoT Core\. For example, it verifies your board can send and receive MQTT messages and process them correctly\. The tests run by IDT for Amazon FreeRTOS are defined in the [Amazon FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

The tests run as embedded applications that are flashed onto your board\. The application binary images include Amazon FreeRTOS, the semiconductor vendor’s ported Amazon FreeRTOS interfaces, and board device drivers\. The purpose of the tests is to verify the ported Amazon FreeRTOS interfaces function correctly on top of the device drivers\. 

IDT for Amazon FreeRTOS generates test reports that you can submit to AWS IoT to add your hardware to the AWS Partner Device Catalog\. For more information, see [AWS Device Qualification Program](https://aws.amazon.com/partners/dqp/)\.

IDT for Amazon FreeRTOS runs on a host computer \(Windows, macOS, or Linux\) that is connected to the board to be tested\. IDT executes test cases and aggregates results\. It also provides a command line interface to manage test execution\.

In addition to testing devices, IDT for Amazon FreeRTOS creates resources \(for example, AWS IoT things, Amazon FreeRTOS groups, Lambda functions, and so on\) to facilitate the qualification process\.

To create these resources, IDT for Amazon FreeRTOS uses the AWS credentials configured in the `config.json` to make API calls on your behalf\. These resources are provisioned at various times during a test\.

When you run IDT for Amazon FreeRTOS on your host computer, it performs the following steps:

1. Loads and validates your device and credentials configuration\.

1. Performs selected tests with the required local and cloud resources\.

1. Cleans up local and cloud resources\.

1. Generates tests reports that indicate if your board passed the tests required for qualification\.

The following diagram shows the test infrastructure setup:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/devicetester_afr.png)
# AWS IoT Device Tester for FreeRTOS<a name="device-tester-for-freertos-ug"></a>

AWS IoT Device Tester \(IDT\) is a downloadable testing framework that lets you validate IoT devices\. You can use AWS IoT Device Tester for FreeRTOS to run the FreeRTOS qualification suite, and to create and run custom test suites for your devices\. 

IDT for FreeRTOS runs on a host computer \(Windows, macOS, or Linux\) that is connected to the board to be tested\. IDT executes test cases and aggregates results\. It also provides a command line interface to manage test execution\.

## FreeRTOS qualification suite<a name="idt-frq-overview"></a>

Use IDT for FreeRTOS qualification to verify that the FreeRTOS operating system works locally on your device and can communicate with AWS IoT\. Specifically, it verifies that the porting layer interfaces for the FreeRTOS libraries are implemented correctly\. It also performs end\-to\-end tests with AWS IoT Core\. For example, it verifies your board can send and receive MQTT messages and process them correctly\. The tests run by IDT for FreeRTOS are defined in the [FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

IDT for FreeRTOS generates test reports that you can submit to AWS IoT to add your hardware to the AWS Partner Device Catalog\. For more information, see [AWS Device Qualification Program](https://aws.amazon.com/partners/dqp/)\.

The following diagram shows the test infrastructure setup for FreeRTOS qualification\.

![\[Flowchart that shows how AWS IoT Core interacts with your computer and the microcontroller.\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/devicetester_afr.png)<a name="test-resources-desc"></a>

IDT for FreeRTOS organizes test resources into test suites and test groups:
+ A test suite is the set of test groups used to verify that a device works with particular versions of FreeRTOS\.
+ A test group is the set of individual tests related to a particular feature, such as BLE and MQTT messaging\.

For more information, see [AWS IoT Device Tester for FreeRTOS test suite versions](idt-test-suite-versions.md)\. 

## Custom test suites<a name="idt-custom-tests-overview"></a>

<a name="idt-byotc"></a>Starting in IDT v4\.0\.0, IDT for FreeRTOS combines a standardized configuration setup and result format with a test suite environment that enables you to develop custom test suites for your devices and device software\. You can add custom tests for your own internal validation or provide them to your customers for device verification\.

How you configure custom test suites determines the setting configurations that you must provide to your users to run your custom test suites\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
# AWS IoT Device Tester for FreeRTOS<a name="device-tester-for-freertos-ug"></a>

The IDT for FreeRTOS is a tool to qualify data throughput rate with the FreeRTOS operating system\. The device tester \(IDT\) first opens a USB or UART connection to a device\. It then flashes an image of FreeRTOS configured to test the device functionality under various conditions\. AWS IoT Device Tester suites are extensible and IDT is used for customer AWS IoT test orchestration\. 

IDT for FreeRTOS runs on a host computer \(Windows, macOS, or Linux\) that is connected to the device being tested\. IDT configures and orchestrates test cases, and aggregates results\. It also provides a command line interface to manage test execution\.

## FreeRTOS qualification suite<a name="idt-frq-overview"></a>

IDT for FreeRTOS verifies the port of FreeRTOS on your micro\-controller, and if it can communicate effectively with AWS IoT in a reliable and secure manner\. Specifically, it verifies if the porting layer interfaces for FreeRTOS libraries are implemented correctly\. It also performs end\-to\-end tests with AWS IoT Core\. For example, it verifies if your board can send and receive MQTT messages and process them correctly\.

IDT provides the following FreeRTOS qualification suite versions: 
+ FreeRTOS qualification suite 1\.0 \(FRQ 1\.0\)—IDT versions that use FRQ 1\.0 suites run the tests that are defined in the [FRQ GitHub repository](https://github.com/aws/amazon-freertos)\. 
+  FreeRTOS qualification suite 2\.0 \(FRQ 2\.0\)—IDT versions that use FRQ 2\.0 suites run the tests defined in [FRQ 2\.0 GitHub repository](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests)\. 

IDT for FreeRTOS generates test reports that you can submit to AWS Partner Network \(APN\) for inclusion of your FreeRTOS devices in the AWS Partner Device Catalog\. For more information, see [AWS Device Qualification Program](https://aws.amazon.com/partners/dqp/)\.

The following diagram shows the test infrastructure setup for FreeRTOS qualification\.

![\[Flowchart that shows how AWS IoT Core interacts with your computer and the microcontroller.\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/devicetester_afr.png)<a name="test-resources-desc"></a>

IDT for FreeRTOS organizes test resources into test suites and test groups:
+ A test suite is the set of test groups used to verify that a device works with particular versions of FreeRTOS\.
+ A test group is the set of individual test cases related to a particular feature, such as BLE and MQTT messaging\.

For more information, see [Test suite versions](idt-test-suite-versions.md)

## Custom test suites<a name="idt-custom-tests-overview"></a>

<a name="idt-byotc-afr"></a>IDT for FreeRTOS combines a standardized configuration setup and result format with a test suite environment\. This environment lets you develop custom test suites for your devices and device software\. You can add custom tests for your own internal validation, or provide them to your customers for device verification\.

How you configure custom test suites determines the setting configurations that you must provide to your users to run your custom test suites\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
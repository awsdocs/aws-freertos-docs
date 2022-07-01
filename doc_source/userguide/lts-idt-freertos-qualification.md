# Use IDT with FreeRTOS qualification suite 2\.0 \(FRQ 2\.0\)<a name="lts-idt-freertos-qualification"></a>

The FreeRTOS qualification suite 2\.0 is an updated version of FreeRTOS qualification suite\. We recommend developers to use FRQ 2\.0 because it consists of relevant test cases to qualify devices that run FreeRTOS Long Term Support \(LTS\) libraries\. 

IDT for FreeRTOS verifies the port of FreeRTOS on your micro\-controller, and if it communicates effectively with AWS IoT\. Specifically, it verifies the porting layer interfaces with the FreeRTOS libraries, and if FreeRTOS test repositories are implemented correctly\. It also performs end\-to\-end tests with AWS IoT Core\. The tests run by IDT for FreeRTOS are defined in the [FreeRTOS GitHub repository](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests)\.

IDT for FreeRTOS run tests as embedded applications that it flashes on the microcontroller device under test\. The application binary images include FreeRTOS, the ported FreeRTOS interfaces, and board device drivers\. The purpose of the tests is to verify that the ported FreeRTOS interfaces function correctly on top of your device drivers\.

IDT for FreeRTOS generates test reports that you can submit to AWS IoT to get your hardware listed on the AWS Partner Device Catalog\. For more information, see [AWS Device Qualification Program](https://aws.amazon.com/partners/dqp/)\.

IDT for FreeRTOS runs on a host computer \(Windows, macOS, or Linux\) that is connected to the device under testing\. IDT configures and orchestrates test cases and aggregates results\. It also provides a command line interface to manage running the tests\.

In order to test your device, IDT for FreeRTOS creates resources such as AWS IoT things, FreeRTOS groups, Lambda functions\. To create these resources, IDT for FreeRTOS uses the AWS credentials configured in the `config.json` to make API calls on your behalf\. These resources are provisioned at various times during a test\.

When you run IDT for FreeRTOS on your host computer, it performs the following steps:

1. Loads and validates your device and credentials configuration\.

1. Performs selected tests with the required local and cloud resources\.

1. Cleans up local and cloud resources\.

1. Generates tests reports that indicate if your board passed the tests required for qualification\.

**Topics**
+ [Prerequisites](lts-idt-dev-tester-prereqs.md)
+ [Preparing to test your microcontroller board for the first time](lts-qual-steps.md)
+ [Running the FreeRTOS qualification 2\.0 suite](lts-run-tests.md)
+ [Understanding results and logs](lts-results-logs.md)
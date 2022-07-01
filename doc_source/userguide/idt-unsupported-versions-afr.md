# Unsupported IDT versions for FreeRTOS<a name="idt-unsupported-versions-afr"></a>

This section lists unsupported versions of IDT for FreeRTOS\. Unsupported versions do not receive bug fixes or updates\. For more information, see [Support policy for AWS IoT Device Tester for FreeRTOS](idt-support-policy.md)\.

The following versions of IDT\-FreeRTOS are no longer supported\.IDT v4\.5\.5 and test suite version 2\.1\.1 for FreeRTOS 202112\.00 and 202012\.04\-LTS \(uses FreeRTOS LTS libraries\)

  

**Release notes**
+ Supports testing against FreeRTOS 202112\.00 or FreeRTOS 202012\.04\-LTS that uses FreeRTOS LTS libraries\.

  For more information about what's included in the FreeRTOS 202012\.04\-LTS release, see the [CHANGELOG\.md](https://github.com/FreeRTOS/FreeRTOS-LTS/blob/202012-LTS/CHANGELOG.md) file on GitHub\.
+ Adds new test group `FullCloudIoT` which tests the board against AWS IoT Core Device Advisor\.
+ Resolved an issue affecting the FreeRTOSVersion and FreeRTOSIntegrity test cases\.
+ Minor bug fixes and improvements\.

  

**Test suite versions**
+ FRQ\_2\.1\.1
  + Released 2022\.06\.06IDT v4\.5\.5 and test suite version 2\.1\.0 for FreeRTOS 202107\.00 \(202112\.00 and 202012\.04\-LTS \(uses FreeRTOS LTS libraries\)

  

**Release notes**
+ Supports testing against FreeRTOS 202112\.00 or FreeRTOS 202012\.04\-LTS that uses FreeRTOS LTS libraries\.

  For more information about what's included in the FreeRTOS 202012\.04\-LTS release, see the [CHANGELOG\.md](https://github.com/FreeRTOS/FreeRTOS-LTS/blob/202012-LTS/CHANGELOG.md) file on GitHub\.
+ Adds new test group `FullCloudIoT` which tests the board against AWS IoT Core Device Advisor\.
+ Minor bug fixes and improvements\.

  

**Test suite versions**
+ FRQ\_2\.1\.0
  + Released 2022\.05\.31IDT v4\.3\.0 and test suite version 1\.6\.1 for FreeRTOS 202107\.00 \(uses FreeRTOS 202107\.00 LTS libraries\)

  

**Release notes**
+ Supports FreeRTOS 202107\.00 that uses FreeRTOS 202107\.00 LTS libraries\. For more information about what's included in the FreeRTOS 202107\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202107.00/CHANGELOG.md) file on GitHub\. 
+ Adds the ability to configure and run AWS IoT Device Tester for FreeRTOS through a web based user interface\. See [Use the IDT for FreeRTOS user interface to run the FreeRTOS qualification suite](device-tester-ui.md) to get started\.

  

**Test suite versions**
+ FRQ\_1\.6\.1
  + Released 2021\.07\.26IDT v4\.1\.0 and test suite version 1\.6\.0 for FreeRTOS 202107\.00

  

**Release notes**
+ Supports FreeRTOS 202107\.00\. For more information about what's included in the FreeRTOS 202107\.00 release, see the [ CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202107.00/CHANGELOG.md) file on GitHub\. 
+ Removes the following test cases from OTA qualification:
  + OTA Agent
  + OTA Missing Filename
  + OTA Max Configured Number of Blocks
+ Removes the OTA Dataplane `Both` test group from OTA Qualification\. In the [`device.json` file](qual-steps.md#cfg-dt-dp), the `OTADataPlaneProtocol` configuration now accepts only `HTTP` or `MQTT` as supported values\.
+ Implements the following changes to the `freertosFileConfiguration` configuration in the [`userdata.json` file](qual-steps.md#cfg-dt-ud) for changes to the FreeRTOS source code:
  + Changes the file name that is specified for `otaAgentTestsConfig` and `otaAgentDemosConfig` from `aws_ota_agent_config.h` to `ota_config.h`\.
  + Adds a new `otaDemosConfig` optional configuration to specify the file path to the new `ota_demo_config.h` file\.
+ Adds a new field `testStartDelayms` to `userdata.json` to specify a delay between the time a device is flashed to run a FreeRTOS test group and when it starts running tests\. The value should be given in milliseconds\. This delay can be used to give IDT a chance to connect so that no test output is missed\.

  

**Test suite versions**
+ FRQ\_1\.6\.0
  + Released 2021\.07\.21IDT v4\.0\.1 and test suite version 1\.4\.1 for FreeRTOS 202012\.00

  

**Release notes**
+ Supports FreeRTOS 202012\.00 that uses FreeRTOS LTS libraries\. For more information about what's included in the FreeRTOS 202012\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202012.00/CHANGELOG.md) file in GitHub\.
+ Introduces additional OTA \(Over\-the\-air\) E2E \(end\-to\-end\) test cases\.
+ Supports qualification of development boards running FreeRTOS 202012\.00 that use FreeRTOS LTS libraries\.
+ Adds support for qualification of FreeRTOS development boards using cellular connectivity\.
+ Fixes a bug in the echo server configuration\.
+ Enables you to develop and run your own custom test suites using AWS IoT Device Tester for FreeRTOS\. For more information, see [Use IDT to develop and run your own test suites](idt-custom-tests.md)\.
+ Provides code signed IDT applications, so you don't need to grant permissions when you run it under Windows or macOS\.
+ Refined the BLE test result parsing logic\.

  

**Test suite versions**
+ FRQ\_1\.4\.1
  + Released 2021\.01\.19\.IDT v3\.4\.0 and test suite version 1\.3\.0 for FreeRTOS 202011\.01

  

**Release notes**
+ Supports FreeRTOS 202011\.01\. For more information about what's included in the FreeRTOS 202011\.01 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202011.01/CHANGELOG.md) file in GitHub\.
+ Fixed bug where 'RSA' was not a valid PKCS11 configuration option\.
+ Fixed bug where Amazon S3 buckets aren't cleaned up correctly after OTA tests\.
+ Updates to support the new test cases inside of the FullMQTT test group\.

  

**Test suite versions**
+ FRQ\_1\.3\.0
  + Released 2020\.11\.05\.

**IDT v3\.3\.0 and Test Suite version 1\.2\.0 for FreeRTOS 202007\.00**

**Release notes**
+ Supports FreeRTOS 202007\.00\. For more information about what's included in the FreeRTOS 202007\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202007.00/CHANGELOG.md) file in GitHub\.
+ New end\-to\-end tests to validate Over The Air \(OTA\) update suspend and resume feature\.
+ Fixed bug causing users in eu\-central\-1 Region to be unable to pass config validation for OTA tests\.
+ Added `--update-idt` parameter to the `run-suite` command\. You can use this option to set the response for the IDT update prompt\.
+ Added `--update-managed-policy` parameter to the `run-suite` command\. You can use this option to set the response for the managed policy update prompt\.
+ Internal improvements and bug fixes, including:
  + For automatic test suite updates, improvements to config file upgrade\.

**Test suite versions**
+ FRQ\_1\.2\.0
  + Released 2020\.09\.17\.
  + Internal improvements and bug fixes, including:
    + For OTA, the Disconnect And Resume test now properly uses the MQTT dataplane if MQTT is configured\.
    + For BLE, you can now change the password of the Raspberry Pi image\.

**IDT v3\.0\.2 for FreeRTOS 202002\.00**

  

**Release notes**
+ Supports FreeRTOS 202002\.00\. For more information about what's included in the FreeRTOS 202002\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202002.00/CHANGELOG.md) file in GitHub\.
+ Adds automatic update of test suites within IDT\. IDT can now download the latest test suites that are available for your FreeRTOS version\. With this feature, you can:
  + Download the latest test suites using the `upgrade-test-suite` command\. 
  + Download the latest test suites by setting a flag when you start IDT\.

    Use the `-u flag` option where *flag* can be '`y`' to always download or '`n`' to use the existing version\.

    When there are multiple test suite versions available, the latest version is used unless you specify a test suite ID when starting IDT\.
  + Use the new `list-supported-versions` option to list the FreeRTOS and test suite versions that are supported by the installed version of IDT\.
  + List test cases in a group and run individual tests\.

  Test suites are versioned using a `major`\.`minor`\.`patch` format starting from 1\.0\.0\.
+ Adds the `list-supported-products` command – Lists the FreeRTOS and test suite versions that are supported by the installed version of IDT\.
+ Adds `list-test-cases` command – Lists the test cases that are available in a test group\.
+ Adds the `test-id` option for the `run-suite` command – Use this option to run individual test cases in a test group\.

  

**Test suite versions**
+ FRQ\_1\.0\.1

**IDT v1\.7\.1 for FreeRTOS 202002\.00**

  

**Release notes**
+ Supports FreeRTOS 202002\.00\. For more information about what's included in the FreeRTOS 202002\.00 release, see the [ CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202002.00/CHANGELOG.md) file in GitHub\.
+ Supports the custom code signing method for over\-the\-air \(OTA\) end\-to\-end test cases so that you can use your own code signing commands and scripts to sign OTA payloads\.
+ Adds a precheck for serial ports before the start of tests\. Tests will fail quickly with improved error messaging if the serial port is misconfigured in the `device.json` file\.
+ Added an [AWS Managed Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) `AWSIoTDeviceTesterForFreeRTOSFullAccess` with permissions required to run AWS IoT Device Tester\. If new releases require additional permissions, we add them to this managed policy so that you don't have to manually update your IAM permissions\.
+ The file named `AFQ_Report.xml` in the results directory is now `FRQ_Report.xml`\.

**IDT v1\.6\.2 for FreeRTOS 201912\.00**

  

**Release notes**
+ Supports FreeRTOS 201912\.00\.
+ Supports optional tests for OTA over HTTPS to qualify your FreeRTOS development boards\.
+ Supports AWS IoT ATS endpoint in testing\.
+ Supports capability to inform users on latest IDT version before start of test suite\.

**IDT v1\.5\.2 for FreeRTOS 201910\.00**

  

**Release notes**
+ Supports qualification of FreeRTOS devices with secure element \(onboard key\)\.
+ Supports configurable echo server ports for Secure Sockets and Wi\-Fi test groups\.
+ Supports timeout multiplier flag to increase timeouts, which is useful when you troubleshoot for timeout\-related errors\.
+ Added bug fix for log parsing\.
+ Supports iot ats endpoint in testing\.

**IDT v1\.4\.1 for FreeRTOS 201908\.00**

  

**Release notes**
+ Added support for new PKCS11 library and test case updates\.
+ Introduced actionable error codes\. For more information, see [IDT error codes](dt-afr-troubleshooting.md#idt-error-codes)
+ Updated IAM policy used to run IDT\. IDT v1\.3\.2 for FreeRTOS 201906\.00 Major

  

**Release notes**
+ Added support for testing Bluetooth Low Energy \(BLE\)\.
+ Improved user experience for IDT command line interface \(CLI\) commands\.
+ Updated IAM policy used to run IDT\.IDT\-FreeRTOS v1\.2

FreeRTOS Versions:  
+ FreeRTOS v1\.4\.9
+ FreeRTOS v1\.4\.8

  

**Release Notes:**
+ Added support for FreeRTOS v1\.4\.8 and v1\.4\.9\.
+ Added support for testing FreeRTOS devices with the CMAKE build system\.IDT\-FreeRTOS v1\.1

FreeRTOS Versions:  
+ FreeRTOS v1\.4\.7IDT\-FreeRTOS v1\.0

FreeRTOS Versions:  
+ FreeRTOS v1\.4\.6
+ FreeRTOS v1\.4\.5
+ FreeRTOS v1\.4\.4
+ FreeRTOS v1\.4\.3
+ FreeRTOS v1\.4\.2
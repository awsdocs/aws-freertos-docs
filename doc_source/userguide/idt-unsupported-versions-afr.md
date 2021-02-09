# Unsupported IDT versions for FreeRTOS<a name="idt-unsupported-versions-afr"></a>

This section lists unsupported versions of IDT for FreeRTOS\. Unsupported versions do not receive bug fixes or updates\. For more information, see [Support policy for AWS IoT Device Tester for FreeRTOS](idt-support-policy.md)\.

The following versions of IDT\-FreeRTOS are no longer supported\.

**IDT v3\.0\.2 for FreeRTOS 202002\.00**
+ IDT for FreeRTOS: [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_3.0.2.zip)
+ IDT for FreeRTOS: [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_3.0.2.zip)
+ IDT for FreeRTOS: [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_3.0.2.zip)

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
+ IDT for FreeRTOS: [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_1.7.1.zip)
+ IDT for FreeRTOS: [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_1.7.1.zip)
+ IDT for FreeRTOS: [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_1.7.1.zip)

**Release notes**
+ Supports FreeRTOS 202002\.00\. For more information about what's included in the FreeRTOS 202002\.00 release, see the [ CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202002.00/CHANGELOG.md) file in GitHub\.
+ Supports the custom code signing method for over\-the\-air \(OTA\) end\-to\-end test cases so that you can use your own code signing commands and scripts to sign OTA payloads\.
+ Adds a precheck for serial ports before the start of tests\. Tests will fail quickly with improved error messaging if the serial port is misconfigured in the `device.json` file\.
+ Added an [ AWS Managed Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) `AWSIoTDeviceTesterForFreeRTOSFullAccess` with permissions required to run AWS IoT Device Tester\. If new releases require additional permissions, we add them to this managed policy so that you don't have to manually update your IAM permissions\.
+ The file named `AFQ_Report.xml` in the results directory is now `FRQ_Report.xml`\.

**IDT v1\.6\.2 for FreeRTOS 201912\.00**
+ IDT for FreeRTOS: [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_linux_1.6.2.zip)
+ IDT for FreeRTOS: [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_mac_1.6.2.zip)
+ IDT for FreeRTOS: [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_win_1.6.2.zip)

**Release notes**
+ Supports FreeRTOS 201912\.00\.
+ Supports optional tests for OTA over HTTPS to qualify your FreeRTOS development boards\.
+ Supports AWS IoT ATS endpoint in testing\.
+ Supports capability to inform users on latest IDT version before start of test suite\.

**IDT v1\.5\.2 for FreeRTOS 201910\.00**
+ IDT for Amazon FreeRTOS: [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_linux_1.5.2.zip)
+ IDT for Amazon FreeRTOS: [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_mac_1.5.2.zip)
+ IDT for Amazon FreeRTOS: [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_win_1.5.2.zip)

**Release notes**
+ Supports qualification of FreeRTOS devices with secure element \(onboard key\)\.
+ Supports configurable echo server ports for Secure Sockets and Wi\-Fi test groups\.
+ Supports timeout multiplier flag to increase timeouts which comes in handy when you troubleshoot for timeout related errors\.
+ Added bug fix for log parsing\.
+ Supports iot ats endpoint in testing\.

**IDT v1\.4\.1 for FreeRTOS 201908\.00**
+ IDT for Amazon FreeRTOS: [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_linux_1.4.1.zip)
+ IDT for Amazon FreeRTOS: [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_mac_1.4.1.zip)
+ IDT for Amazon FreeRTOS: [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_win_1.4.1.zip)

**Release notes**
+ Added support for new PKCS11 library and test case updates\.
+ Introduced actionable error codes\. For more information, see [IDT error codes](dt-afr-troublshooting.md#idt-error-codes)
+ Updated IAM policy used to run IDT\. 

**IDT v1\.3\.2 for FreeRTOS 201906\.00 Major**
+ IDT for FreeRTOS: [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_linux_1.3.2.zip)
+ IDT for FreeRTOS: [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_mac_1.3.2.zip)
+ IDT for FreeRTOS: [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/afr/devicetester_afreertos_win_1.3.2.zip)

**Release notes**
+ Added support for testing Bluetooth Low Energy \(BLE\)\.
+ Improved user experience for IDT command line interface \(CLI\) commands\.
+ Updated IAM policy used to run IDT\.

**IDT\-FreeRTOS v1\.2**
+ FreeRTOS Versions:
  + FreeRTOS v1\.4\.9
  + FreeRTOS v1\.4\.8
+ Release Notes:
  + Added support for FreeRTOS v1\.4\.8 and v1\.4\.9\.
  + Added support for testing FreeRTOS devices with the CMAKE build system\.

**IDT\-FreeRTOS v1\.1**
+ FreeRTOS Versions:
  + FreeRTOS v1\.4\.7

**IDT\-FreeRTOS v1\.0**
+ FreeRTOS Versions:
  + FreeRTOS v1\.4\.6
  + FreeRTOS v1\.4\.5
  + FreeRTOS v1\.4\.4
  + FreeRTOS v1\.4\.3
  + FreeRTOS v1\.4\.2
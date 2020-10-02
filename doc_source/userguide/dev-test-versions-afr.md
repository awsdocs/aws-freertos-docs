# Supported versions of AWS IoT Device Tester for FreeRTOS<a name="dev-test-versions-afr"></a>

This topic lists supported versions of IDT for FreeRTOS\. As a best practice, we recommend that you use the latest version of IDT for FreeRTOS that supports your target version of FreeRTOS\. Each version of IDT for FreeRTOS has one or more corresponding versions of FreeRTOS\. New releases of FreeRTOS might require you to download a new version of IDT for FreeRTOS\. 

By downloading the software, you agree to the IDT for FreeRTOS License Agreement\. 

## Latest version of AWS IoT Device Tester for FreeRTOS<a name="idt-latest-version-afr"></a>

Use the following links to download the latest version of IDT for FreeRTOS\.

**IDT v3\.3\.0 and Test Suite version 1\.2\.0 for FreeRTOS 202007\.00**
+ IDT v3\.3\.0 with test suite FRQ\_1\.2\.0 for [ Linux](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_linux_3.3.0.zip)
+ IDT v3\.3\.0 with test suite FRQ\_1\.2\.0 for [ macOS](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_mac_3.3.0.zip)
+ IDT v3\.3\.0 with test suite FRQ\_1\.2\.0 for [ Windows](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_win_3.3.0.zip)

**Note**  
We don't recommend that multiple users run IDT from a shared location, such as an NFS directory or a Windows network shared folder\. This may result in crashes or data corruption\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

**Release notes**
+ Supports FreeRTOS 202007\.00\. For more information about what's included in the FreeRTOS 202007\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202007.00/CHANGELOG.md) file in GitHub\.
+ New end to end tests to validate Over The Air \(OTA\) update suspend and resume feature\.
+ Fixed bug causing users in eu\-central\-1 region to be unable to pass config validation for OTA tests\.
+ Added `--update-idt` parameter to the `run-suite` command\. You can use this option to set the response for the IDT update prompt\.
+ Added `--update-managed-policy` parameter to the `run-suite` command\. You can use this option to set the response for the managed policy update prompt\.
+ Internal improvements and bug fixes, including:
  + For automatic test suite updates, improvements to config file upgrade\.

**Test suite versions**
+ FRQ\_1\.2\.0
  + Released 2020\.09\.17\.
  + Internal improvements and bug fixes, including:
    + For OTA, the Disconnect And Resume test now properly uses the MQTT dataplane if MQTT is configured\.
    + For BLE, you can now change the password of the RaspberryPi image if you so choose\.

## Earlier IDT versions for FreeRTOS<a name="idt-prev-versions-afr"></a>

The following earlier versions of IDT for FreeRTOS are also supported\.

**IDT v3\.0\.2 for FreeRTOS 202002\.00**
+ IDT for FreeRTOS: [ Linux](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_linux_3.0.2.zip)
+ IDT for FreeRTOS: [ macOS](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_mac_3.0.2.zip)
+ IDT for FreeRTOS: [ Windows](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_win_3.0.2.zip)

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
+ IDT for FreeRTOS: [ Linux](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_linux_1.7.1.zip)
+ IDT for FreeRTOS: [ macOS](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_mac_1.7.1.zip)
+ IDT for FreeRTOS: [ Windows](https://d232ctwt5kahio.cloudfront.net/freertos/devicetester_freertos_win_1.7.1.zip)

**Release notes**
+ Supports FreeRTOS 202002\.00\. For more information about what's included in the FreeRTOS 202002\.00 release, see the [ CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202002.00/CHANGELOG.md) file in GitHub\.
+ Supports the custom code signing method for over\-the\-air \(OTA\) end\-to\-end test cases so that you can use your own code signing commands and scripts to sign OTA payloads\.
+ Adds a precheck for serial ports before the start of tests\. Tests will fail quickly with improved error messaging if the serial port is misconfigured in the `device.json` file\.
+ Added an [ AWS Managed Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) `AWSIoTDeviceTesterForFreeRTOSFullAccess` with permissions required to run AWS IoT Device Tester\. If new releases require additional permissions, we add them to this managed policy so that you don't have to manually update your IAM permissions\.
+ The file named `AFQ_Report.xml` in the results directory is now `FRQ_Report.xml`\.

**IDT v1\.6\.2 for FreeRTOS 201912\.00**
+ IDT for FreeRTOS: [ Linux](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_linux_1.6.2.zip)
+ IDT for FreeRTOS: [ macOS](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_mac_1.6.2.zip)
+ IDT for FreeRTOS: [ Windows](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_win_1.6.2.zip)

**Release notes**
+ Supports FreeRTOS 201912\.00\.
+ Supports optional tests for OTA over HTTPS to qualify your FreeRTOS development boards\.
+ Supports AWS IoT ATS endpoint in testing\.
+ Supports capability to inform users on latest IDT version before start of test suite\.

For more information, see [Support policy for AWS IoT Device Tester for FreeRTOS](idt-support-policy.md)\.
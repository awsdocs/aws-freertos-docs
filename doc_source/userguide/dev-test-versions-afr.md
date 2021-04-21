# Supported versions of AWS IoT Device Tester for FreeRTOS<a name="dev-test-versions-afr"></a>

This topic lists supported versions of IDT for FreeRTOS\. As a best practice, we recommend that you use the latest version of IDT for FreeRTOS that supports your target version of FreeRTOS\. Each version of IDT for FreeRTOS has one or more corresponding versions of FreeRTOS\. New releases of FreeRTOS might require you to download a new version of IDT for FreeRTOS\. 

By downloading the software, you agree to the IDT for FreeRTOS License Agreement\. 

## Latest version of AWS IoT Device Tester for FreeRTOS<a name="idt-latest-version-afr"></a>

Use the following links to download the latest version of IDT for FreeRTOS\.

**IDT v4\.0\.2 and test suite version 1\.5\.0 for FreeRTOS 202012\.00 \(uses FreeRTOS 202012\.00 LTS libraries\)**
+ IDT v4\.0\.2 with test suite FRQ\_1\.5\.0 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_4.0.2.zip)
+ IDT v4\.0\.2 with test suite FRQ\_1\.5\.0 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_4.0.2.zip)
+ IDT v4\.0\.2 with test suite FRQ\_1\.5\.0 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_4.0.2.zip)

**Note**  
We don't recommend that multiple users run IDT from a shared location, such as an NFS directory or a Windows network shared folder\. This practice might result in crashes or data corruption\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

**Release notes**
+ Support for qualification of devices with locked\-down credentials on a Hardware Security Module\.
+ Minor bug fixes and improvements\.

**Test suite versions**
+ FRQ\_1\.5\.0
  + Released 2021\.04\.16\.

## Earlier IDT versions for FreeRTOS<a name="idt-prev-versions-afr"></a>

The following earlier versions of IDT for FreeRTOS are also supported\.

**IDT v4\.0\.1 and test suite version 1\.4\.1 for FreeRTOS 202012\.00 \(uses FreeRTOS 202012\.00 LTS libraries\)**
+ IDT v4\.0\.1 with test suite FRQ\_1\.4\.1 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_4.0.1.zip)
+ IDT v4\.0\.1 with test suite FRQ\_1\.4\.1 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_4.0.1.zip)
+ IDT v4\.0\.1 with test suite FRQ\_1\.4\.1 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_4.0.1.zip)

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
  + Released 2021\.01\.19\.

**IDT v3\.4\.0 and test suite version 1\.3\.0 for FreeRTOS 202011\.01**
+ IDT v3\.4\.0 with test suite FRQ\_1\.3\.0 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_3.4.0.zip)
+ IDT v3\.4\.0 with test suite FRQ\_1\.3\.0 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_3.4.0.zip)
+ IDT v3\.4\.0 with test suite FRQ\_1\.3\.0 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_3.4.0.zip)

**Release notes**
+ Supports FreeRTOS 202011\.01\. For more information about what's included in the FreeRTOS 202011\.01 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202011.01/CHANGELOG.md) file in GitHub\.
+ Fixed bug where 'RSA' was not a valid PKCS11 configuration option\.
+ Fixed bug where Amazon S3 buckets aren’t cleaned up correctly after OTA tests\.
+ Updates to support the new test cases inside of the FullMQTT test group\.

**Test suite versions**
+ FRQ\_1\.3\.0
  + Released 2020\.11\.05\.

**IDT v3\.3\.0 and Test Suite version 1\.2\.0 for FreeRTOS 202007\.00**
+ IDT v3\.3\.0 with test suite FRQ\_1\.2\.0 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_3.3.0.zip)
+ IDT v3\.3\.0 with test suite FRQ\_1\.2\.0 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_3.3.0.zip)
+ IDT v3\.3\.0 with test suite FRQ\_1\.2\.0 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_3.3.0.zip)

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
    + For BLE, you can now change the password of the Raspberry Pi image\.

For more information, see [Support policy for AWS IoT Device Tester for FreeRTOS](idt-support-policy.md)\.
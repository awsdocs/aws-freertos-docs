# Supported versions of AWS IoT Device Tester for FreeRTOS<a name="dev-test-versions-afr"></a>

This topic lists supported versions of IDT for FreeRTOS\. As a best practice, we recommend that you use the latest version of IDT for FreeRTOS that supports your target version of FreeRTOS\. Each version of IDT for FreeRTOS has one or more corresponding versions of FreeRTOS\. New releases of FreeRTOS might require you to download a new version of IDT for FreeRTOS\. 

By downloading the software, you agree to the [IDT for FreeRTOS License Agreement](https://docs.aws.amazon.com/freertos/latest/userguide/AWS+IoT+Device+Tester+License+Agreement.pdf)\. 

## Latest version of AWS IoT Device Tester for FreeRTOS<a name="idt-latest-version-afr"></a>

Use the following links to download the latest versions of IDT for FreeRTOS\.

  

**IDT v4\.5\.6 and test suite version 2\.1\.2 for FreeRTOS 202112\.00 and 202012\.04\-LTS \(uses FreeRTOS LTS libraries\)**
+ IDT v4\.5\.6 with test suite FRQ\_2\.1\.2 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.6_testsuite_2.1.2_linux.zip)
+ IDT v4\.5\.6 with test suite FRQ\_2\.1\.2 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.6_testsuite_2.1.2_mac.zip)
+ IDT v4\.5\.6 with test suite FRQ\_2\.1\.2 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.6_testsuite_2.1.2_win.zip)

  

**Release notes**
+ Supports testing against FreeRTOS 202112\.00 or FreeRTOS 202012\.04\-LTS that uses FreeRTOS LTS libraries\.

  For more information about what's included in the FreeRTOS 202012\.04\-LTS release, see the [CHANGELOG\.md](https://github.com/FreeRTOS/FreeRTOS-LTS/blob/202012-LTS/CHANGELOG.md) file on GitHub\.
+ Adds new test group `FullCloudIoT` which tests the board against AWS IoT Core Device Advisor\.
+ Resolved an issue affecting the OTA E2E test cases\.
+ Minor bug fixes and improvements\.

  

**Test suite versions**
+ FRQ\_2\.1\.2
  + Released 2022\.06\.29IDT v4\.5\.2 and test suite version 1\.6\.2 for FreeRTOS 202107\.00 \(uses FreeRTOS 202107\.00 LTS libraries\)

  
+ IDT v4\.5\.2 with test suite FRQ\_1\.6\.2 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.2_testsuite_1.6.2_linux.zip)
+ IDT v4\.5\.2 with test suite FRQ\_1\.6\.2 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.2_testsuite_1.6.2_mac.zip)
+ IDT v4\.5\.2 with test suite FRQ\_1\.6\.2 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.2_testsuite_1.6.2_win.zip)

  

**Release notes**
+ Supports FreeRTOS 202107\.00 that uses FreeRTOS 202107\.00 LTS libraries\. For more information about what's included in the FreeRTOS 202107\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202107.00/CHANGELOG.md) file on GitHub\. 
+ Implements the new IDT test orchestrator for configuring custom test suites\. For more information, see [Configure the IDT test orchestrator](idt-test-orchestrator.md) 
+ Minor bug fixes and improvements\.

  

**Test suite versions**
+ FRQ\_1\.6\.2
  + Released 2022\.01\.25

**Note**  
We don't recommend that multiple users run IDT from a shared location, such as an NFS directory or a Windows network shared folder\. This practice might result in crashes or data corruption\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

## Earlier IDT versions for FreeRTOS<a name="idt-prev-versions-afr"></a>

The following earlier versions of IDT for FreeRTOS are also supported\.IDT v4\.5\.4 and test suite version 2\.0\.0 for FreeRTOS 202112\.00 and 202012\.04\-LTS \(uses FreeRTOS LTS libraries\)

  
+ IDT v4\.5\.4 with test suite FRQ\_2\.0\.0 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.4_testsuite_2.0.0_linux.zip)
+ IDT v4\.5\.4 with test suite FRQ\_2\.0\.0 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.4_testsuite_2.0.0_mac.zip)
+ IDT v4\.5\.4 with test suite FRQ\_2\.0\.0 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.4_testsuite_2.0.0_win.zip)

  

**Release notes**
+ Supports testing against FreeRTOS 202112\.00 or FreeRTOS 202012\.04\-LTS that uses FreeRTOS LTS libraries\.

  For more information about what's included in the FreeRTOS 202012\.04\-LTS release, see the [CHANGELOG\.md](https://github.com/FreeRTOS/FreeRTOS-LTS/blob/202012-LTS/CHANGELOG.md) file on GitHub\.
+ Removes the requirement to qualify boards using only versions of Amazon FreeRTOS from the `aws/amazon-freertos` GitHub repository\.
+ Minor bug fixes and improvements\.

  

**Test suite versions**
+ FRQ\_2\.0\.0
  + Released 2022\.05\.09IDT v4\.0\.3 and test suite version 1\.5\.1 for FreeRTOS 202012\.00 \(uses FreeRTOS 202012\.00 LTS libraries\)

  
+ IDT v4\.0\.3 with test suite FRQ\_1\.5\.1 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_linux_4.0.3.zip)
+ IDT v4\.0\.3 with test suite FRQ\_1\.5\.1 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_mac_4.0.3.zip)
+ IDT v4\.0\.3 with test suite FRQ\_1\.5\.1 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_win_4.0.3.zip)

  

**Release notes**
+ Support for qualification of devices with locked\-down credentials on a Hardware Security Module\.
+ Minor bug fixes and improvements\.

  

**Test suite versions**
+ FRQ\_1\.5\.1
  + Released 2021\.07\.30\.

For more information, see [Support policy for AWS IoT Device Tester for FreeRTOS](idt-support-policy.md)\.
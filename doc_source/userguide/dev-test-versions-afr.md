# Supported versions of AWS IoT Device Tester for FreeRTOS<a name="dev-test-versions-afr"></a>

This topic lists supported versions of IDT for FreeRTOS\. As a best practice, we recommend that you use the latest version of IDT for FreeRTOS that supports your target version of FreeRTOS\. Each version of IDT for FreeRTOS has one or more corresponding versions of FreeRTOS\. New releases of FreeRTOS might require you to download a new version of IDT for FreeRTOS\. 

By downloading the software, you agree to the IDT for FreeRTOS License Agreement\. 

## Latest version of AWS IoT Device Tester for FreeRTOS<a name="idt-latest-version-afr"></a>

Use the following links to download the latest version of IDT for FreeRTOS\.

**IDT v4\.5\.2 and test suite version 1\.6\.2 for FreeRTOS 202107\.00 \(uses FreeRTOS 202107\.00 LTS libraries\)**
+ IDT v4\.5\.2 with test suite FRQ\_1\.6\.2 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.2_testsuite_1.6.2_linux.zip)
+ IDT v4\.5\.2 with test suite FRQ\_1\.6\.2 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.2_testsuite_1.6.2_mac.zip)
+ IDT v4\.5\.2 with test suite FRQ\_1\.6\.2 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.5.2_testsuite_1.6.2_win.zip)

**Note**  
We don't recommend that multiple users run IDT from a shared location, such as an NFS directory or a Windows network shared folder\. This practice might result in crashes or data corruption\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

**Release notes**
+ Supports FreeRTOS 202107\.00 that uses FreeRTOS 202107\.00 LTS libraries\. For more information about what's included in the FreeRTOS 202107\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202107.00/CHANGELOG.md) file on GitHub\. 
+ Implements the new IDT test orchestrator for configuring custom test suites\. For more information, see [Configure the IDT test orchestrator](idt-test-orchestrator.md) 
+ Minor bug fixes and improvements\.

**Test suite versions**
+ FRQ\_1\.6\.2
  + Released 2022\.01\.25

## Earlier IDT versions for FreeRTOS<a name="idt-prev-versions-afr"></a>

The following earlier versions of IDT for FreeRTOS are also supported\.

**IDT v4\.3\.0 and test suite version 1\.6\.1 for FreeRTOS 202107\.00 \(uses FreeRTOS 202107\.00 LTS libraries\)**
+ IDT v4\.3\.0 with test suite FRQ\_1\.6\.1 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.3.0_testsuite_1.6.1_linux.zip)
+ IDT v4\.3\.0 with test suite FRQ\_1\.6\.1 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.3.0_testsuite_1.6.1_mac.zip)
+ IDT v4\.3\.0 with test suite FRQ\_1\.6\.1 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.3.0_testsuite_1.6.1_win.zip)

**Release notes**
+ Supports FreeRTOS 202107\.00 that uses FreeRTOS 202107\.00 LTS libraries\. For more information about what's included in the FreeRTOS 202107\.00 release, see the [CHANGELOG\.md](https://github.com/aws/amazon-freertos/blob/202107.00/CHANGELOG.md) file on GitHub\. 
+ Adds the ability to configure and run AWS IoT Device Tester for FreeRTOS through a web based user interface\. See [Use the IDT for FreeRTOS user interface to run the FreeRTOS qualification suite](device-tester-ui.md) to get started\.

**Test suite versions**
+ FRQ\_1\.6\.1
  + Released 2021\.07\.26

**IDT v4\.1\.0 and test suite version 1\.6\.0 for FreeRTOS 202107\.00**
+ IDT v4\.1\.0 with test suite FRQ\_1\.6\.0 for [ Linux](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.1.0_testsuite_1.6.0_linux.zip)
+ IDT v4\.1\.0 with test suite FRQ\_1\.6\.0 for [ macOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.1.0_testsuite_1.6.0_mac.zip)
+ IDT v4\.1\.0 with test suite FRQ\_1\.6\.0 for [ Windows](https://docs.aws.amazon.com/freertos/latest/userguide/freertos/devicetester_freertos_4.1.0_testsuite_1.6.0_win.zip)

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
  + Released 2021\.07\.21

**IDT v4\.0\.3 and test suite version 1\.5\.1 for FreeRTOS 202012\.00 \(uses FreeRTOS 202012\.00 LTS libraries\)**
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
# What is FreeRTOS?<a name="what-is-freertos"></a>

Developed in partnership with the world's leading chip companies over a 15\-year period, and now downloaded every 175 seconds, FreeRTOS is a market\-leading real\-time operating system \(RTOS\) for microcontrollers and small microprocessors\. Distributed freely under the MIT open source license, FreeRTOS includes a kernel and a growing set of libraries suitable for use across all industry sectors\. FreeRTOS is built with an emphasis on reliability and ease of use\.

FreeRTOS includes libraries for connectivity, security, and over\-the\-air \(OTA\) updates\. FreeRTOS also includes demo applications that show FreeRTOS features on [qualified boards](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

FreeRTOS is an open\-source project\. You can download the source code, contribute changes or enhancements, or report issues on the GitHub site at [ https://github\.com/aws/amazon\-freertos](https://github.com/aws/amazon-freertos)\. We release FreeRTOS code under the MIT open source license, so you can use it in commercial and personal projects\.

We also welcome contributions to the FreeRTOS documentation \(*FreeRTOS User Guide*, *FreeRTOS Porting Guide*, and *FreeRTOS Qualification Guide*\)\. The markdown source for the documentation is available at [https://github\.com/awsdocs/aws\-freertos\-docs](https://github.com/awsdocs/aws-freertos-docs)\. It is released under the Creative Commons \(CC BY\-ND\) license\.

## Downloading FreeRTOS source code<a name="freertos-mds-projects-github"></a>

You can download versions of FreeRTOS that are configured for FreeRTOS\-qualified platforms from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. For a list of qualified platforms, see [FreeRTOS\-qualified hardware platforms](#freertos-hardware) or the [FreeRTOS Partners](https://aws.amazon.com/freertos/partners/) website\.

You can also clone or download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/main/README.md) file for instructions\. 

## FreeRTOS versioning<a name="freertos-versioning"></a>

The FreeRTOS kernel and components are released individually and use semantic versioning\. Integrated FreeRTOS releases are made periodically\.  All releases use date\-based versioning with the format YYYYMM\.NN, where:
+ Y represents the year\.
+ M represents the month\.
+ N represents the release order within the designated month \(00 being the first release\)\.

For example, a second release in July 2021 would be 202107\.01\. 

Previously, FreeRTOS releases used semantic versioning for major releases\. Although it has moved to date\-based versioning \(FreeRTOS 1\.4\.8 updated to FreeRTOS AWS Reference Integrations 201906\.00\), the FreeRTOS kernel and each individual FreeRTOS library still retain semantic versioning\. In semantic versioning, the version number itself \(X\.Y\.Z\) indicates whether the release is a major, minor, or point release\.  You can use the semantic version of a library to assess the scope and impact of a new release on your application\.

LTS releases are maintained differently than other release types\. Major and minor releases are frequently updated with new features in addition to defect resolutions\. LTS releases are only updated with changes to address critical defects and security vulnerabilities\. No new features are introduced in a given LTS release after launch\. They are maintained for at least three calendar years after release, and provide device manufacturers the option to use a stable baseline as opposed to a more dynamic baseline represented by major and minor releases\.

## FreeRTOS architecture<a name="freertos-architecture"></a>

FreeRTOS is typically flashed to devices as a single compiled image with all of the components required for device applications\. This image combines functionality for the applications written by the embedded developer, the software libraries provided by Amazon, the FreeRTOS kernel, and drivers and board support packages \(BSPs\) for the hardware platform\. Independent of the individual microcontroller being used, embedded application developers can expect the same standardized interfaces to the FreeRTOS kernel and all FreeRTOS software libraries\.



## FreeRTOS\-qualified hardware platforms<a name="freertos-hardware"></a>

The following hardware platforms are qualified for FreeRTOS:
+ [ ATECC608A Zero Touch Provisioning Kit for AWS IoT](https://devices.amazonaws.com/detail/a3G0L00000AANvOUAX/ATECC608a-Zero-Touch-Provisioning-Kit-for-AWS-IoT)
+ [Cypress CYW943907AEVAL1F Development Kit](https://devices.amazonaws.com/detail/a3G0L00000AAPg0UAH/CYW943907AEVAL1F)
+ [Cypress CYW954907AEVAL1F Development Kit](https://devices.amazonaws.com/detail/a3G0L00000AAPg5UAH/CYW954907AEVAL1F)
+ [ Cypress CY8CKIT\-064S0S2\-4343W Kit](https://www.cypress.com/documentation/development-kitsboards/psoc-64-standard-secure-aws-wi-fi-bt-pioneer-kit-cy8ckit)
+ [Espressif ESP32\-DevKitC](https://devices.amazonaws.com/detail/a3G0L00000AANtjUAH/ESP32-DevKitC)
+ [Espressif ESP\-WROVER\-KIT](https://devices.amazonaws.com/detail/a3G0L00000AANtlUAH/ESP-WROVER-KIT)
+ [Espressif ESP\-WROOM\-32SE](https://devices.amazonaws.com/detail/a3G0h0000077nRtEAI/ESP32-WROOM-32SE)
+ [Espressif ESP32\-S2\-Saola\-1](https://devices.amazonaws.com/detail/a3G0h00000AkFngEAF/ESP32-S2-Saola-1)
+ [ Infineon XMC4800 IoT Connectivity Kit](https://devices.amazonaws.com/detail/a3G0L00000AANsbUAH/XMC4800-IoT-Amazon-FreeRTOS-Connectivity-Kit-WiFi)
+ [Marvell MW320 AWS IoT Starter Kit](https://devices.amazonaws.com/detail/a3G0h000000OaRnEAK/Marvell-MW320)
+ [Marvell MW322 AWS IoT Starter Kit](https://devices.amazonaws.com/detail/a3G0h000000OblKEAS/Marvell-MW322)
+ [MediaTek MT7697Hx Development Kit](https://devices.amazonaws.com/detail/a3G0L00000AAOmPUAX/MT7697Hx-Development-Kit)
+ [ Microchip Curiosity PIC32MZEF Bundle](https://devices.amazonaws.com/detail/a3G0L00000AANscUAH/Curiosity-PIC32MZ-EF-Amazon-FreeRTOS-Bundle)
+ [Nordic nRF52840\-DK](https://devices.amazonaws.com/detail/a3G0L00000AANtrUAH/nRF52840-Development-Kit)
+ [NuMaker\-IoT\-M487](https://devices.amazonaws.com/detail/a3G0h000000Tg9cEAC/NuMaker-IoT-M487)
+ [NXP LPC54018 IoT Module](https://devices.amazonaws.com/detail/a3G0L00000AANtAUAX/LPC54018-IoT-Solution)
+ [ OPTIGA Trust X Security Solution](https://devices.amazonaws.com/detail/a3G0h000007712QEAQ/OPTIGA%E2%84%A2-Trust-X-Security-Solution)
+ [ Renesas RX65N RSK IoT Module](https://devices.amazonaws.com/detail/a3G0L00000AAOkeUAH/Renesas-Starter-Kit+-for-RX65N-2MB)
+ [ STMicroelectronicsSTM32L4 Discovery Kit IoT Node](https://devices.amazonaws.com/detail/a3G0L00000AANsWUAX/STM32L4-Discovery-Kit-IoT-Node)
+ [ Texas Instruments CC3220SF\-LAUNCHXL](https://devices.amazonaws.com/detail/a3G0L00000AANtaUAH/SimpleLink-Wi-Fi®-CC3220SF-Wireless-Microcontroller-LaunchPad-Development-Kit)
+ Microsoft Windows 7 or later, with at least a dual core and a hard\-wired Ethernet connection
+ [Xilinx Avnet MicroZed Industrial IoT Kit](https://devices.amazonaws.com/detail/a3G0L00000AANtqUAH/MicroZed-IIoT-Bundle-with-Amazon-FreeRTOS)

Qualified devices are also listed on the [AWS Partner Device Catalog](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

For information about qualifying a new device, see the [FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.

## Development workflow<a name="development-workflow"></a>

You start development by downloading FreeRTOS\. You unzip the package and import it into your IDE\. You can then develop an application on your selected hardware platform and manufacture and deploy these devices using the development process appropriate for your device\. Deployed devices can connect to the AWS IoT service or AWS IoT Greengrass as part of a complete IoT solution\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/afr-getting-started-workflow.png)

## Additional resources<a name="resources"></a>

These resources might be helpful to you\.
+ Additional [FreeRTOS Documentation](https://www.freertos.org/Documentation/RTOS_book.html) is available on [freertos\.org](https://www.freertos.org) including the [FreeRTOS v10\.0\.0 Reference Manual](https://www.freertos.org/fr-content-src/uploads/2018/07/FreeRTOS_Reference_Manual_V10.0.0.pdf)\.
+ For questions about FreeRTOS for the FreeRTOS engineering team, you can open an issue [on the FreeRTOS GitHub page](https://github.com/aws/amazon-freertos/issues)\.
+ For technical questions about FreeRTOS visit the [FreeRTOS Community Forums](https://forums.freertos.org/)\.
+ For more information about connecting devices to AWS IoT, see the [AWS IoT Core Developer Guide](https://docs.aws.amazon.com/iot/latest/developerguide) and the chapter on [Device Provisioning](https://docs.aws.amazon.com/iot/latest/developerguide/iot-provision.html) in that guide\.
+ For technical support for AWS, visit the [AWS Support Center](https://aws.amazon.com/support)\.
+ For questions about AWS billing, account services, events, abuse, or other issues with AWS, visit the [Contact Us](https://aws.amazon.com/contact-us/) page\.
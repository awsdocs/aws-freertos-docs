# FreeRTOS Porting<a name="porting-guide"></a>

## What is FreeRTOS?<a name="porting-what-is-afr"></a>

Developed in partnership with the world's leading chip companies over a 15\-year period, and now downloaded every 175 seconds, FreeRTOS is a market\-leading real\-time operating system \(RTOS\) for microcontrollers and small microprocessors\. Distributed freely under the MIT open source license, FreeRTOS includes a kernel and a growing set of libraries suitable for use across all industry sectors\. FreeRTOS is built with an emphasis on reliability and ease of use\.

FreeRTOS includes libraries for connectivity, security, and over\-the\-air \(OTA\) updates\. FreeRTOS also includes demo applications that show FreeRTOS features on qualified boards\.

FreeRTOS is an open\-source project\. You can download the source code, contribute changes or enhancements, or report issues on the GitHub site at [ https://github\.com/aws/amazon\-freertos](https://github.com/aws/amazon-freertos)\. We release FreeRTOS code under the MIT open source license, so you can use it in commercial and personal projects\.

We also welcome contributions to the FreeRTOS documentation \(*FreeRTOS User Guide*, *FreeRTOS Porting Guide*, and *FreeRTOS Qualification Guide*\)\. The markdown source for the documentation is available at [https://github\.com/awsdocs/aws\-freertos\-docs](https://github.com/awsdocs/aws-freertos-docs)\. It is released under the Creative Commons \(CC BY\-ND\) license\.

The FreeRTOS kernel and components are released individually and use semantic versioning\. Integrated FreeRTOS releases are made periodically\. The three types of FreeRTOS releases are major, minor, and long\-term support \(LTS\)\. A major denotation indicates the addition of new features or significant updates to multiple libraries\. All releases use date\-based versioning with the format YYYYMM\.NN, where:
+ Y represents the year\.
+ M represents the month\.
+ N represents the release order within the designated month \(00 being the first release\)\.

For example, a second release in June 2019 would be 201906\.01\. 

Previously, FreeRTOS releases used semantic versioning for major releases\. Although it has moved to date\-based versioning \(FreeRTOS 1\.4\.8 updated to FreeRTOS 201906\.00\), the FreeRTOS kernel and each individual FreeRTOS library still retains semantic versioning\. In semantic versioning, the version number itself \(X\.Y\.Z\) indicates whether the release is a major, minor, or point release\. This can create situations where semantic versioning indicates a major release based on changes that don't affect an individual application\. You can use the semantic version of a library to assess the scope and impact of a new release on your application\.

LTS releases are maintained differently than other release types\. Major and minor releases are frequently updated with new features in addition to defect resolutions\. LTS releases are only updated with changes to address critical defects and security vulnerabilities\. No new features are introduced in a given LTS release after launch\. They are maintained for at least three calendar years after release, and provide device manufacturers the option to use a stable baseline as opposed to a more dynamic baseline represented by major and minor releases\.

## Porting FreeRTOS to Your IoT Device<a name="porting-to-your-device"></a>

Before a microcontroller board can run FreeRTOS, some FreeRTOS code must be ported to the device's hardware\. Basic kernel ports should refer to the [ FreeRTOS porting guide](https://www.freertos.org/FreeRTOS-porting-guide.html) on [www\.freertos\.org](https://www.freertos.org/)\. For ports intending to include the FreeRTOS libraries for security, connectivity, etc\., the following instructions build on the kernel port\.

**To port FreeRTOS to your device**

1. Follow the instructions in [Downloading FreeRTOS for Porting](porting-download.md) to download the latest version of FreeRTOS for porting\.

1. Follow the instructions in [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md) to configure the files and folders in your FreeRTOS download for porting and testing\.

1. Follow the instructions in [Porting the FreeRTOS Libraries](afr-porting.md) to port the FreeRTOS libraries to your device\. Each porting topic includes instructions on testing the ports\.

### System Requirements<a name="porting-requirements"></a>

The device that you port to FreeRTOS must be a microcontroller board that meets the following minimum requirements:
+ 25MHz processing speed
+ 64KB RAM
+ 128KB program memory per executable image stored on the MCU
+ \(If [Porting the OTA Library](afr-porting-ota.md)\) Two executable images stored on the MCU

### Porting Older Versions of FreeRTOS<a name="porting-older"></a>

If you are porting an older version of FreeRTOS, go to the [`amazon-freertos` GitHub repository](https://github.com/aws/amazon-freertos), and checkout the version of FreeRTOS that you are porting by its version tag\. The qualification and testing documentation will be in PDF format, in the [https://github.com/aws/amazon-freertos/tree/master/tests](https://github.com/aws/amazon-freertos/tree/master/tests) folder\. See the table below for the qualification and testing documentation history\.


**Revision History of FreeRTOS Porting and Qualification Documentation**  

|  Date  |  Porting and Qualification Documentation Version  |  Change History  |  FreeRTOS Version  | 
| --- | --- | --- | --- | 
|  February 18, 2020  |  [202002\.00](https://github.com/aws/amazon-freertos/blob/202002.00/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [202002\.00](https://github.com/aws/amazon-freertos/blob/202002.00/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [202002\.00](https://github.com/aws/amazon-freertos/tree/202002.00)  | 
|  December 17, 2019  |  [201912\.00](https://github.com/aws/amazon-freertos/blob/201912.00/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201912\.00](https://github.com/aws/amazon-freertos/blob/201912.00/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [201912\.00](https://github.com/aws/amazon-freertos/tree/201912.00)  | 
|  October 29, 2019  |  [201910\.00](https://github.com/aws/amazon-freertos/blob/201910.00/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201910\.00](https://github.com/aws/amazon-freertos/blob/201910.00/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [201910\.00](https://github.com/aws/amazon-freertos/tree/201910.00)  | 
|  August 26, 2019  |  [201908\.00](https://github.com/aws/amazon-freertos/blob/201908.00/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201908\.00](https://github.com/aws/amazon-freertos/blob/201908.00/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [201908\.00](https://github.com/aws/amazon-freertos/tree/201908.00)  | 
|  June 17, 2019  |  [201906\.00](https://github.com/aws/amazon-freertos/blob/201906.00_Major/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201906\.00](https://github.com/aws/amazon-freertos/blob/201906.00_Major/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [201906\.00 Major](https://github.com/aws/amazon-freertos/tree/201906.00_Major)  | 
|  May 21, 2019  |  [1\.4\.8](https://github.com/aws/amazon-freertos/blob/v1.4.8/tests/afreertos-pg.pdf) \(Porting Guide\) [1\.4\.8](https://github.com/aws/amazon-freertos/blob/v1.4.8/tests/afreertos-qg.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.8](https://github.com/aws/amazon-freertos/tree/v1.4.8)  | 
|  February 25, 2019  |  [1\.1\.6](https://github.com/aws/amazon-freertos/blob/v1.4.7/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.5](https://github.com/aws/amazon-freertos/tree/v1.4.5) [1\.4\.6](https://github.com/aws/amazon-freertos/tree/v1.4.6) [1\.4\.7](https://github.com/aws/amazon-freertos/tree/v1.4.7)  | 
|  December 27, 2018  |  [1\.1\.5](https://github.com/aws/amazon-freertos/blob/v1.4.6/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.5](https://github.com/aws/amazon-freertos/tree/v1.4.5) [1\.4\.6](https://github.com/aws/amazon-freertos/tree/v1.4.6)  | 
|  December 12, 2018  |  [1\.1\.4](https://github.com/aws/amazon-freertos/blob/v1.4.5/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.5](https://github.com/aws/amazon-freertos/tree/v1.4.5)  | 
|  November 26, 2018  |  [1\.1\.3](https://github.com/aws/amazon-freertos/blob/v1.4.4/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.4](https://github.com/aws/amazon-freertos/tree/v1.4.4)  | 
|  November 7, 2018  |  [1\.1\.2](https://github.com/aws/amazon-freertos/blob/v1.4.3/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.3](https://github.com/aws/amazon-freertos/tree/v1.4.3)  | 
|  October 8, 2018  |  [1\.1\.1](https://github.com/aws/amazon-freertos/blob/v1.4.2/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.2](https://github.com/aws/amazon-freertos/tree/v1.4.2)  | 
|  August 27, 2018  |  [1\.1\.0](https://github.com/aws/amazon-freertos/blob/v1.4.0/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.4\.0](https://github.com/aws/amazon-freertos/tree/v1.4.0) [1\.4\.1](https://github.com/aws/amazon-freertos/tree/v1.4.1)  | 
|  August 9, 2018  |  [1\.0\.1](https://github.com/aws/amazon-freertos/blob/v1.3.2/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [1\.3\.1](https://github.com/aws/amazon-freertos/tree/v1.3.1) [1\.3\.2](https://github.com/aws/amazon-freertos/tree/v1.3.2)  | 
|  July 31, 2018  |  [1\.0\.0](https://github.com/aws/amazon-freertos/blob/v1.3.0/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide-V1.0.0.pdf)  |  Initial version of the FreeRTOS Qualification Program Guide  |  [1\.3\.0](https://github.com/aws/amazon-freertos/tree/v1.3.0)  | 

### Porting FAQs<a name="afr-porting-faqs"></a>

*What is a FreeRTOS port?*  
A FreeRTOS port is a board\-specific implementation of APIs for the required FreeRTOS libraries and the FreeRTOS that your platform supports\. The port enables the APIs to work on the board, and implements the required integration with the device drivers and BSPs that are provided by the platform vendor\. Your port should also include any configuration adjustments \(e\.g\. clock rate, stack size, heap size\) that are required by the board\.

*My device does not support Wi\-Fi, Bluetooth Low Energy, or over\-the\-air \(OTA\) updates\. Are all libraries required to port FreeRTOS?*  
The primary requirement for porting FreeRTOS connectivity libraries is that your device can connect to the cloud\. If, for example, you can connect to the cloud across a secure ethernet connection, FreeRTOS, the Wi\-Fi library is not a required\. Keep in mind that some test and demo applications will not work without all of the libraries ported\.

*Can I reach an "[echo server](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-echo-server.html)" from two different networks \(for example, from two subnets across 2 different access points\)? *  
An echo server is required to pass the TCP/IP and TLS port tests\. The echo server must be reachable from the network that a board is connected to\. Please consult your IT support to enable routing across subnets if you need devices on different subnets to communicate with a single echo server\.

*What network ports need to be open to run the FreeRTOS port tests?*  
The following network connections are required to run the FreeRTOS port tests:      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)

If you have questions about porting that are not answered on this page or in the rest of the FreeRTOS Porting Guide, please [contact the FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.
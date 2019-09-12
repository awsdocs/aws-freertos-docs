# Amazon FreeRTOS Porting<a name="porting-guide"></a>

## What is Amazon FreeRTOS?<a name="porting-what-is-afr"></a>

Amazon FreeRTOS is a real\-time operating system that augments the [ FreeRTOS kernel](https://freertos.org/index.html) with libraries for connectivity, security, and over\-the\-air \(OTA\) updates\. Amazon FreeRTOS also includes demo applications that show Amazon FreeRTOS features on [qualified boards](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

Amazon FreeRTOS is an open\-source project\. You can download the source code, contribute changes or enhancements, or report issues on the GitHub site at [ https://github\.com/aws/amazon\-freertos](https://github.com/aws/amazon-freertos)\. We release Amazon FreeRTOS code under the MIT open source license, so you can use it in commercial and personal projects\.

We also welcome contributions to the Amazon FreeRTOS documentation \(*Amazon FreeRTOS User Guide*, *Amazon FreeRTOS Porting Guide*, and *Amazon FreeRTOS Qualification Guide*\)\. The markdown source for the documentation is available at [https://github\.com/awsdocs/aws\-freertos\-docs](https://github.com/awsdocs/aws-freertos-docs)\. It is released under the Creative Commons \(CC BY\-ND\) license\.

The three types of Amazon FreeRTOS releases are major, minor, and long\-term support \(LTS\)\. A major denotation indicates the addition of new features or significant updates to multiple libraries\. All releases use date\-based versioning with the format YYYYMM\.NN, where:
+ Y represents the year\.
+ M represents the month\.
+ N represents the release order within the designated month \(00 being the first release\)\.

For example, a second release in June 2019 would be 201906\.01\. 

Previously, Amazon FreeRTOS used semantic versioning for major releases\. Although it has moved to date\-based versioning \(Amazon FreeRTOS 1\.4\.8 updated to Amazon FreeRTOS 201906\.00\), the FreeRTOS kernel and each individual Amazon FreeRTOS library still retain semantic versioning\. In semantic versioning, the version number itself \(X\.Y\.Z\) indicates whether the release is a major, minor, or point release\. This can create situations where semantic versioning indicates a major release based on changes that don’t affect an individual application\. You can use the semantic version of a library to assess the scope and impact of a new release on your application\.

LTS releases are maintained differently than other release types\. Major and minor releases are frequently updated with new features in addition to defect resolutions\. LTS releases are only updated with changes to address critical defects and security vulnerabilities\. No new features are introduced in a given LTS release after launch\. They are maintained for at least three calendar years after release, and provide device manufacturers the option to use a stable baseline as opposed to a more dynamic baseline represented by major and minor releases\.

## Porting Amazon FreeRTOS to Your Device<a name="porting-to-your-device"></a>

Before a microcontroller board can run Amazon FreeRTOS, some Amazon FreeRTOS code must be ported to the device's hardware\.

**To port Amazon FreeRTOS to your device**

1. Follow the instructions in [Downloading Amazon FreeRTOS for Porting](porting-download.md) to download the latest version of Amazon FreeRTOS for porting\.

1. Follow the instructions in [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md) to configure the files and folders in your Amazon FreeRTOS download for porting and testing\.

1. Follow the instructions in [Porting the Amazon FreeRTOS Libraries](afr-porting.md) to port the Amazon FreeRTOS libraries to your device\. Each porting topic includes instructions on testing the ports\.

### System Requirements<a name="porting-requirements"></a>

The device that you port to Amazon FreeRTOS must be a microcontroller board that meets the following minimum requirements:
+ 25MHz processing speed
+ 64KB RAM
+ 128KB program memory per executable image stored on the MCU
+ \(If [Porting the OTA Library](afr-porting-ota.md)\) Two executable images stored on the MCU

### Porting Older Versions of Amazon FreeRTOS<a name="porting-older"></a>

If you are porting an older version of Amazon FreeRTOS, go to the [`amazon-freertos` GitHub repository](https://github.com/aws/amazon-freertos), and checkout the version of Amazon FreeRTOS that you are porting by its version tag\. The qualification and testing documentation will be in PDF format, in the [https://github.com/aws/amazon-freertos/tree/master/tests](https://github.com/aws/amazon-freertos/tree/master/tests) folder\. See the table below for the qualification and testing documentation history\.


**Revision History of Amazon FreeRTOS Porting and Qualification Documentation**  

|  Date  |  Porting and Qualification Documentation Version  |  Change History  |  Amazon FreeRTOS Version  | 
| --- | --- | --- | --- | 
|  August 26, 2019  |  [201908\.00](https://github.com/aws/amazon-freertos/blob/201908.00/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201908\.00](https://github.com/aws/amazon-freertos/blob/201908.00/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)  |  [201908\.00 Major](https://github.com/aws/amazon-freertos/tree/201908.00)  | 
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
|  July 31, 2018  |  [1\.0\.0](https://github.com/aws/amazon-freertos/blob/v1.3.0/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide-V1.0.0.pdf)  |  Initial version of the Amazon FreeRTOS Qualification Program Guide  |  [1\.3\.0](https://github.com/aws/amazon-freertos/tree/v1.3.0)  | 

### Porting FAQs<a name="afr-porting-faqs"></a>

*What is an Amazon FreeRTOS port?*  
An Amazon FreeRTOS port is a board\-specific implementation of APIs for the required Amazon FreeRTOS libraries and the Amazon FreeRTOS that your platform supports\. The port enables the APIs to work on the board, and implements the required integration with the device drivers and BSPs that are provided by the platform vendor\. Your port should also include any configuration adjustments \(e\.g\. clock rate, stack size, heap size\) that are required by the board\.

*My device does not support Wi\-Fi, Bluetooth Low Energy, or over\-the\-air \(OTA\) updates\. Are all libraries required to port Amazon FreeRTOS?*  
The primary requirement for Amazon FreeRTOS is that your device can connect to the AWS Cloud\. If, for example, you can connect to the cloud across a secure ethernet connection, Amazon FreeRTOS, the Wi\-Fi library is not a required\. Keep in mind that some test and demo applications will not work without all of the libraries ported\.

*Can I reach an "[echo server](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-echo-server.html)" from two different networks \(for example, from two subnets across 2 different access points\)?*  
An echo server is required to pass the TCP/IP and TLS port tests\. The echo server must be reachable from the network that a board is connected to\. Please consult your IT support to enable routing across subnets if you need devices on different subnets to communicate with a single echo server\.

*What network ports need to be open to run the Amazon FreeRTOS port tests?*  
The following network connections are required to run the Amazon FreeRTOS port tests:      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)

If you have questions about porting that are not answered on this page or in the rest of the Amazon FreeRTOS Porting Guide, please [contact the Amazon FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.
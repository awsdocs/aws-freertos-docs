# Amazon FreeRTOS Qualification<a name="afr-qualification"></a>

## What is Amazon FreeRTOS?<a name="what-is-afr-qualification"></a>

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

## What Is Amazon FreeRTOS Qualification?<a name="afr-qualification"></a>

Amazon Partner Network Partners can use the AWS Device Qualification Program to officially qualify a microcontroller \(MCU\) development board for Amazon FreeRTOS\.

Amazon FreeRTOS qualification gives developers a reliable and consistent experience across a range of qualified, MCU\-based development boards\. By abstracting some of the complexity of embedded development, Amazon FreeRTOS qualification makes it possible for developers to focus on designing the application code for their product, so they can rapidly evaluate, prototype, and productize IoT solutions\. This qualification process helps bridge the gap between cloud developers and embedded engineers\.

Qualified boards are eligible for listing on the [AWS Partner Device Catalog](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

To qualify a device for Amazon FreeRTOS, you must port Amazon FreeRTOS to your device, and then follow the Device Qualification Program steps\. For information, see the [AWS Device Qualification Program page](https://aws.amazon.com/partners/dqp/) and the [AWS Device Qualification Program Guide](https://partnercentral.awspartner.com/sfc/#version?selectedDocumentId=0690L000005HXjb)\.

For information about qualifying your device for Amazon FreeRTOS, see [Qualifying Your Device](freertos-qualification.md)\.

### Qualification FAQs<a name="afr-qualification-faqs"></a>

If a version of Amazon FreeRTOS is released after I have started porting a previous version, do I need to start over using the latest version?  
Always start porting the latest version of Amazon FreeRTOS\. If we release a new version of Amazon FreeRTOS while you are working on your port, you can contact us with questions about how the release might affect your qualification effort\.

My device does not support Wi\-Fi\. Is a port of the Amazon FreeRTOS Wi\-Fi library required to qualify for Amazon FreeRTOS?  
The primary requirement is that your device can connect to the AWS Cloud\. If your device can connect to the AWS Cloud across a secure ethernet connection, the Wi\-Fi library is not a requirement\.

My device does not support Bluetooth Low Energy or over\-the\-air \(OTA\) updates\. Are ports for these Amazon FreeRTOS libraries required to qualify for Amazon FreeRTOS?  
Bluetooth Low Energy and OTA ports are optional for qualification\.

My board does not have on\-chip TCP/IP functionality\. Is a particular TCP/IP stack required for Amazon FreeRTOS qualification?  
If your board does not have on\-chip TCP/IP functionality, you can use either the FreeRTOS\+TCP TCP/IP stack or version 2\.0\.3 of the lwIP TCP/IP stack to pass TCP/IP qualification requirements\. For more information, see [Porting a TCP/IP Stack](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tcp.html) in the *Amazon FreeRTOS Porting Guide*\.

Is a particular TLS stack required for qualification?  
Amazon FreeRTOS supports mbedTLS and off\-chip TLS implementations, such as those found on some network processors\. No matter which TLS implementation is used by your device's port of Amazon FreeRTOS, the port must pass the Device Tester validation tests for TLS\. For more information, see [Porting the TLS Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tls.html) in the *Amazon FreeRTOS Porting Guide*\.

Does my device need to pass all of the AWS IoT Device Tester validation tests to qualify? Is there a way to qualify without passing all of the tests?  
Your device must pass all of the required validation tests to qualify for Amazon FreeRTOS\. The only exceptions are for Wi\-Fi, Bluetooth Low Energy, and OTA\.

If you have questions about qualification that are not answered on this page or in the rest of the Amazon FreeRTOS Qualification Guide, contact your AWS representative or [the Amazon FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.

### Documentation History<a name="afr-doc-history"></a>


**Revision History of Amazon FreeRTOS Porting and Qualification Documentation**  

|  Date  |  Porting and Qualification Documentation Version  |  Change History  |  Amazon FreeRTOS Version  | 
| --- | --- | --- | --- | 
|  August 26, 2019  |  [201908\.00](https://github.com/aws/amazon-freertos/blob/201908.00/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201908\.00](https://github.com/aws/amazon-freertos/blob/201908.00/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [201908\.00 Major](https://github.com/aws/amazon-freertos/tree/201908.00)  | 
|  June 17, 2019  |  [201906\.00](https://github.com/aws/amazon-freertos/blob/201906.00_Major/doc/freertos_port_qual/aFreeRTOS_Porting_Guide.pdf) \(Porting Guide\) [201906\.00](https://github.com/aws/amazon-freertos/blob/201906.00_Major/doc/freertos_port_qual/aFreeRTOS_Qualification_Guide.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [201906\.00 Major](https://github.com/aws/amazon-freertos/tree/201906.00_Major)  | 
|  May 21, 2019  |  [1\.4\.8](https://github.com/aws/amazon-freertos/blob/v1.4.8/tests/afreertos-pg.pdf) \(Porting Guide\) [1\.4\.8](https://github.com/aws/amazon-freertos/blob/v1.4.8/tests/afreertos-qg.pdf) \(Qualification Guide\)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.8](https://github.com/aws/amazon-freertos/tree/v1.4.8)  | 
|  February 25, 2019  |  [1\.1\.6](https://github.com/aws/amazon-freertos/blob/v1.4.7/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.5](https://github.com/aws/amazon-freertos/tree/v1.4.5) [1\.4\.6](https://github.com/aws/amazon-freertos/tree/v1.4.6) [1\.4\.7](https://github.com/aws/amazon-freertos/tree/v1.4.7)  | 
|  December 27, 2018  |  [1\.1\.5](https://github.com/aws/amazon-freertos/blob/v1.4.6/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.5](https://github.com/aws/amazon-freertos/tree/v1.4.5) [1\.4\.6](https://github.com/aws/amazon-freertos/tree/v1.4.6)  | 
|  December 12, 2018  |  [1\.1\.4](https://github.com/aws/amazon-freertos/blob/v1.4.5/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.5](https://github.com/aws/amazon-freertos/tree/v1.4.5)  | 
|  November 26, 2018  |  [1\.1\.3](https://github.com/aws/amazon-freertos/blob/v1.4.4/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.4](https://github.com/aws/amazon-freertos/tree/v1.4.4)  | 
|  November 7, 2018  |  [1\.1\.2](https://github.com/aws/amazon-freertos/blob/v1.4.3/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.3](https://github.com/aws/amazon-freertos/tree/v1.4.3)  | 
|  October 8, 2018  |  [1\.1\.1](https://github.com/aws/amazon-freertos/blob/v1.4.2/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.2](https://github.com/aws/amazon-freertos/tree/v1.4.2)  | 
|  August 27, 2018  |  [1\.1\.0](https://github.com/aws/amazon-freertos/blob/v1.4.0/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.4\.0](https://github.com/aws/amazon-freertos/tree/v1.4.0) [1\.4\.1](https://github.com/aws/amazon-freertos/tree/v1.4.1)  | 
|  August 9, 2018  |  [1\.0\.1](https://github.com/aws/amazon-freertos/blob/v1.3.2/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/qualificationguide/afr-qualification.html)  |  [1\.3\.1](https://github.com/aws/amazon-freertos/tree/v1.3.1) [1\.3\.2](https://github.com/aws/amazon-freertos/tree/v1.3.2)  | 
|  July 31, 2018  |  [1\.0\.0](https://github.com/aws/amazon-freertos/blob/v1.3.0/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide-V1.0.0.pdf)  |  Initial version of the Amazon FreeRTOS Qualification Program Guide  |  [1\.3\.0](https://github.com/aws/amazon-freertos/tree/v1.3.0)  | 
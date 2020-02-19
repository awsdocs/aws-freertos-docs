# What Is FreeRTOS?<a name="what-is-freertos"></a>

Developed in partnership with the world's leading chip companies over a 15\-year period, and now downloaded every 175 seconds, FreeRTOS is a market\-leading real\-time operating system \(RTOS\) for microcontrollers and small microprocessors\. Distributed freely under the MIT open source license, FreeRTOS includes a kernel and a growing set of libraries suitable for use across all industry sectors\. FreeRTOS is built with an emphasis on reliability and ease of use\.

FreeRTOS includes libraries for connectivity, security, and over\-the\-air \(OTA\) updates\. FreeRTOS also includes demo applications that show FreeRTOS features on [qualified boards](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

FreeRTOS is an open\-source project\. You can download the source code, contribute changes or enhancements, or report issues on the GitHub site at [ https://github\.com/aws/amazon\-freertos](https://github.com/aws/amazon-freertos)\. We release FreeRTOS code under the MIT open source license, so you can use it in commercial and personal projects\.

We also welcome contributions to the FreeRTOS documentation \(*FreeRTOS User Guide*, *FreeRTOS Porting Guide*, and *FreeRTOS Qualification Guide*\)\. The markdown source for the documentation is available at [https://github\.com/awsdocs/aws\-freertos\-docs](https://github.com/awsdocs/aws-freertos-docs)\. It is released under the Creative Commons \(CC BY\-ND\) license\.

The FreeRTOS kernel and components are released individually and use semantic versioning\. Integrated FreeRTOS releases are made periodically\. The three types of FreeRTOS releases are major, minor, and long\-term support \(LTS\)\. A major denotation indicates the addition of new features or significant updates to multiple libraries\. All releases use date\-based versioning with the format YYYYMM\.NN, where:
+ Y represents the year\.
+ M represents the month\.
+ N represents the release order within the designated month \(00 being the first release\)\.

For example, a second release in June 2019 would be 201906\.01\. 

Previously, FreeRTOS releases used semantic versioning for major releases\. Although it has moved to date\-based versioning \(FreeRTOS 1\.4\.8 updated to FreeRTOS 201906\.00\), the FreeRTOS kernel and each individual FreeRTOS library still retain semantic versioning\. In semantic versioning, the version number itself \(X\.Y\.Z\) indicates whether the release is a major, minor, or point release\. This can create situations where semantic versioning indicates a major release based on changes that don't affect an individual application\. You can use the semantic version of a library to assess the scope and impact of a new release on your application\.

LTS releases are maintained differently than other release types\. Major and minor releases are frequently updated with new features in addition to defect resolutions\. LTS releases are only updated with changes to address critical defects and security vulnerabilities\. No new features are introduced in a given LTS release after launch\. They are maintained for at least three calendar years after release, and provide device manufacturers the option to use a stable baseline as opposed to a more dynamic baseline represented by major and minor releases\.

## FreeRTOS Architecture<a name="freertos-architecture"></a>

FreeRTOS is typically flashed to devices as a single compiled image with all of the components required for device applications\. This image combines functionality for the applications written by the embedded developer, the software libraries provided by Amazon, the FreeRTOS kernel, and drivers and board support packages \(BSPs\) for the hardware platform\. Independent of the individual microcontroller being used, embedded application developers can expect the same standardized interfaces to the FreeRTOS kernel and all FreeRTOS software libraries\.



## FreeRTOS Kernel<a name="freertos-kernel"></a>

The FreeRTOS kernel is a real\-time operating system that supports numerous architectures and is ideal for building embedded microcontroller applications\. The kernel provides:
+ A multitasking scheduler\.
+ Multiple memory allocation options \(including the ability to create statically allocated systems\)\.
+ Inter\-task coordination primitives, including task notifications, message queues, multiple types of semaphores, and stream and message buffers\.

For the most up\-to\-date documentation about the FreeRTOS kernel, see [FreeRTOS\.org](https://freertos.org/RTOS.html)\. FreeRTOS\.org offers a number of detailed tutorials and guides about using the FreeRTOS kernel, including a [Quick Start Guide](https://freertos.org/FreeRTOS-quick-start-guide.html#page_top) and the more in\-depth [ Mastering the FreeRTOS Real Time Kernel](https://freertos.org/Documentation/161204_Mastering_the_FreeRTOS_Real_Time_Kernel-A_Hands-On_Tutorial_Guide.pdf)\. For more information about the FreeRTOS kernel in this guide, see [FreeRTOS Kernel Fundamentals](dev-guide-freertos-kernel.md)\.

## FreeRTOS Libraries<a name="freertos-libraries"></a>

FreeRTOS includes libraries that make it possible to:
+ Securely connect devices to the AWS IoT Cloud using MQTT and device shadows\.
+ Discover and connect to AWS IoT Greengrass cores\.
+ Manage Wi\-Fi connections\.
+ Listen for and process [FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)\.

For more information, see [FreeRTOS Libraries](https://docs.aws.amazon.com/freertos/latest/userguide/dev-guide-freertos-libraries.html)\.

## Downloading FreeRTOS Source Code<a name="freertos-mds-projects-github"></a>

You can download versions of FreeRTOS that are configured for FreeRTOS\-qualified platforms from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. For a list of qualified platforms, see [FreeRTOS\-Qualified Hardware Platforms](freertos-hardware.md) or the [FreeRTOS Partners](https://aws.amazon.com/freertos/partners/) website\.

You can also clone or download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. See the [ README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. 

## FreeRTOS Console<a name="freertos-console"></a>

From the [FreeRTOS console](https://console.aws.amazon.com/freertos), you can configure and download a package that contains everything you need to write an application for your microcontroller\-based devices:
+ The FreeRTOS kernel\.
+ FreeRTOS libraries\.
+ Platform support libraries\.
+ Hardware drivers\.

For more information, see [FreeRTOS Console](freertos-ocw.md)\.

## Development Workflow<a name="development-workflow"></a>

You start development by downloading FreeRTOS\. You unzip the package and import it into your IDE\. You can then develop an application on your selected hardware platform and manufacture and deploy these devices using the development process appropriate for your device\. Deployed devices can connect to the AWS IoT service or AWS IoT Greengrass as part of a complete IoT solution\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/afr-getting-started-workflow.png)

## Additional Resources<a name="resources"></a>

These resources might be helpful to you\.
+ For questions about FreeRTOS for the FreeRTOS engineering team, you can open an issue [on the FreeRTOS GitHub page](https://github.com/aws/amazon-freertos/issues)\.
+ For technical questions about FreeRTOS visit the [FreeRTOS Community Forums](https://forums.freertos.org/)\.
+ For technical support for AWS, visit the [AWS Support Center](https://aws.amazon.com/support)\.
+ For questions about AWS billing, account services, events, abuse, or other issues with AWS, visit the [Contact Us](https://aws.amazon.com/contact-us/) page\.
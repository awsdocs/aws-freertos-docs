# What Is Amazon FreeRTOS?<a name="what-is-amazon-freertos"></a>

Amazon FreeRTOS is a real\-time operating system that augments the [FreeRTOS kernel](https://freertos.org/index.html) with libraries for connectivity, security, and over\-the\-air \(OTA\) updates\. Amazon FreeRTOS also includes some demo applications that demonstrate Amazon FreeRTOS features on [qualified boards](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

## Amazon FreeRTOS Architecture<a name="freertos-architecture"></a>

Amazon FreeRTOS is typically flashed to devices as a single compiled image with all of the components required for device applications\. This image combines functionality for the applications written by the embedded developer, the software libraries provided by Amazon, the FreeRTOS kernel, and drivers and board support packages \(BSPs\) for the hardware platform\. Independent of the individual microcontroller being used, embedded application developers can expect the same standardized interfaces to the FreeRTOS kernel and all Amazon FreeRTOS software libraries\.



## FreeRTOS Kernel<a name="freertos-kernel"></a>

The FreeRTOS kernel is a real\-time operating system kernel that supports numerous architectures and is ideal for building embedded microcontroller applications\. The kernel provides:
+ A multitasking scheduler\.
+ Multiple memory allocation options \(including the ability to create statically allocated systems\)\.
+ Inter\-task coordination primitives, including task notifications, message queues, multiple types of semaphores, and stream and message buffers\.

For more information about the FreeRTOS kernel, see [FreeRTOS Kernel Fundamentals](dev-guide-freertos-kernel.md)\.

## Amazon FreeRTOS Libraries<a name="freertos-libraries"></a>

Amazon FreeRTOS includes libraries that make it possible to:
+ Securely connect devices to the AWS IoT Cloud using MQTT and device shadows\.
+ Discover and connect to AWS IoT Greengrass cores\.
+ Manage Wi\-Fi connections\.
+ Listen for and process [Amazon FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)\.

For more information, see [Amazon FreeRTOS Libraries](https://docs.aws.amazon.com/freertos/latest/userguide/dev-guide-freertos-libraries.html)\.

## Downloading Amazon FreeRTOS Source Code<a name="freertos-mds-projects-github"></a>

You can download versions of Amazon FreeRTOS that are configured for Amazon FreeRTOS\-qualified platforms from the [Amazon FreeRTOS console]()\. For a list of qualified platforms, see [Amazon FreeRTOS\-Qualified Hardware Platforms](freertos-hardware.md) or the [Amazon FreeRTOS Partners](https://aws.amazon.com/freertos/partners/) website\.

You can also download Amazon FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\.

## Amazon FreeRTOS Console<a name="freertos-console"></a>

From the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), you can configure and download a package that contains everything you need to write an application for your microcontroller\-based devices:
+ The FreeRTOS kernel\.
+ Amazon FreeRTOS libraries\.
+ Platform support libraries\.
+ Hardware drivers\.

For more information, see [Amazon FreeRTOS Console](freertos-ocw.md)\.

## Development Workflow<a name="development-workflow"></a>

You start development by downloading Amazon FreeRTOS\. You unzip the package and import it into your IDE\. You can then develop an application on your selected hardware platform and manufacture and deploy these devices using the development process appropriate for your device\. Deployed devices can connect to the AWS IoT service or AWS IoT Greengrass as part of a complete IoT solution\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/afr-getting-started-workflow.png)

## Additional Resources<a name="resources"></a>

These resources might be helpful to you\.
+ For questions about Amazon FreeRTOS for the Amazon FreeRTOS engineering team, you can open an issue [on the Amazon FreeRTOS GitHub page](https://github.com/aws/amazon-freertos/issues)\.
+ For technical questions about AWS and Amazon FreeRTOS with the AWS community, visit the [AWS Discussion Forums](https://forums.aws.amazon.com/)\.
+ For technical support for AWS, visit the [AWS Support Center](https://aws.amazon.com/support)\.
+ For questions about AWS billing, account services, events, abuse, or other issues with AWS, visit the [Contact Us](https://aws.amazon.com/contact-us/) page\.
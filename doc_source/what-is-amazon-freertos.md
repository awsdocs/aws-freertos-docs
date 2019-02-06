# What Is Amazon FreeRTOS?<a name="what-is-amazon-freertos"></a>

Amazon FreeRTOS is an operating system for embedded microcontrollers, based on the FreeRTOS kernel\. Amazon FreeRTOS includes libraries for connectivity, security, and over\-the\-air \(OTA\) updates\. Amazon FreeRTOS also includes some applications that demonstrate Amazon FreeRTOS features\.

## Amazon FreeRTOS Architecture<a name="freertos-architecture"></a>

Amazon FreeRTOS is typically flashed to devices as a single compiled image with all of the components required for the device application\. This image combines functionality for the application written by the embedded developer, software libraries provided by Amazon, the FreeRTOS kernel, and drivers and board support packages \(BSPs\) for the hardware platform\. Independent of the individual microcontroller being used, embedded application developers can expect the same standardized interfaces to the FreeRTOS kernel and all Amazon FreeRTOS software libraries\.

You can click on the icons in the diagram below to learn more about the specific components of the Amazon FreeRTOS architecture\.



## The FreeRTOS Kernel<a name="freertos-kernel"></a>

The FreeRTOS kernel is a real\-time operating system kernel that supports numerous architectures and is ideal for building embedded microcontroller applications\. The kernel provides:
+ A multitasking scheduler\.
+ Multiple memory allocation options \(including the ability to create statically allocated systems\)\.
+ Inter\-task coordination primitives, including task notifications, message queues, multiple types of semaphores, and stream and message buffers\.

For more information about the FreeRTOS kernel, see [FreeRTOS Kernel Fundamentals](dev-guide-freertos-kernel.md)\.

## Amazon FreeRTOS Libraries<a name="freertos-libraries"></a>

Amazon FreeRTOS includes libraries that enable you to:
+ Securely connect devices to the AWS IoT cloud using MQTT and device shadows\.
+ Discover and connect to AWS IoT Greengrass cores\.
+ Manage Wi\-Fi connections\.
+ Listen for and process over\-the\-air \(OTA\) updates\.

For more information, see [Amazon FreeRTOS Libraries](https://docs.aws.amazon.com/freertos/latest/userguide/dev-guide-freertos-libraries.html)\.

## Downloading Amazon FreeRTOS Source Code<a name="freertos-mds-projects-github"></a>

You can download the RTOS kernel and software libraries from the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) or from [GitHub](https://github.com/aws/amazon-freertos)\.

## Amazon FreeRTOS Console<a name="freertos-ocw"></a>

The [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) enables you to configure and download a package that contains everything you need to write an application for your microcontroller\-based devices:
+ The FreeRTOS kernel
+ Amazon FreeRTOS libraries
+ Platform support libraries
+ Hardware drivers

You can download a package with a predefined configuration or create your own configuration by selecting your hardware platform and the libraries required for your application\. These configurations are saved in AWS and are available for download at any time\.

### Predefined Amazon FreeRTOS Configurations<a name="freertos-predefined-configurations"></a>

The predefined configurations make it possible for you to get started quickly with the supported use cases without thinking about which libraries are required\. To use a predefined configuration, browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), find the configuration you want to use, and then choose **Download**\.

You can also customize a predefined configuration if you want to change the Amazon FreeRTOS version, hardware platform, or libraries of the configuration\. Customizing a predefined configuration creates a new custom configuration and does not overwrite the predefined configuration in the Amazon FreeRTOS console\.

**To create a custom configuration from a predefined configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis next to the predefined configuration that you want to customize, and then choose **Customize**\.

1. On the **Configure Amazon FreeRTOS Software** page, choose the Amazon FreeRTOS version, hardware platform, and libraries, and give the new configuration a name and a description\.

1. At the bottom of the page, choose **Create and download** to create and download the custom configuration\.

### Custom Amazon FreeRTOS Configurations<a name="freertos-custom-configurations"></a>

Custom configurations allow you to specify your hardware platform, integrated development platform \(IDE\), compiler, and only those RTOS libraries you require\. This leaves more space on your devices for application code\.

**To create a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) and choose **Create new**\.

1. Select the version of Amazon FreeRTOS that you want to use\. The latest version is used by default\.

1. On the **New Software Configuration** page, choose **Select a hardware platform**, and choose one of the prequalified platforms\.

1. Choose the IDE and compiler you want use\.

1. For the Amazon FreeRTOS libraries you require, choose **Add Library**\. If you choose a library that requires another library, it is added for you\. If you want to choose more libraries, choose **Add another library**\.

1. In the **Demo Projects** section, enable one of the demo projects\. This enables the demo in the project files\.

1. In **Name required**, enter a name for your custom configuration\.
**Note**  
Do not use any personally identifiable information in your custom configuration name\.

1. In **Description**, enter a description for your custom configuration\.

1. At the bottom of the page, choose **Create and download** to create and download your custom configuration\.

**To edit a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis next to the configuration you want to edit, and then choose **Edit**\.

1. On the **Configure Amazon FreeRTOS Software** page, you can change your configuration's Amazon FreeRTOS version, hardware platform, libraries, and description\.

1. At the bottom of the page, choose **Save and download** to save and download the configuration\.

**To delete a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis next to the configuration you want to delete, and then choose **Delete**\.

### Quick Connect Workflow<a name="freertos-quick-connect"></a>

The Amazon FreeRTOS console also includes the Quick Connect workflow option for all boards with predefined configurations\. The Quick Connect workflow helps you configure and run Amazon FreeRTOS demo applications for AWS IoT and AWS IoT Greengrass\. To get started, choose the **Predefined configurations** tab, find your board, and then choose **Quick connect** and follow the Quick Connect workflow steps\. 

## Over\-the\-Air Updates<a name="freertos-ota-updates"></a>

Internet\-connected devices can be in use for a long time, and must be updated periodically to fix bugs and improve functionality\. Often these devices must be updated in the field and need to be updated remotely or "over\-the\-air"\. The Amazon FreeRTOS Over\-the\-Air \(OTA\) Update service enables you to:
+ Digitally sign firmware prior to deployment\.
+ Securely deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, reset, or reprovisioned\.
+ Once deployed to devices, verify the authenticity and integrity of the new firmware\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.

When you send files over the air, it is a best practice to digitally sign them so that the devices that receive the files can verify they have not been tampered with en route\. You can use Code Signing for AWS IoT to sign and encrypt your files or you can sign your files with your own code\-signing tools\. For more information about Code Signing for AWS IoT, see the [Code Signing for AWS IoT Developer Guide](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.

For more information about OTA updates, see:
+ [Amazon FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)
+ [Over\-the\-Air Updates Demo Application](ota-demo.md)

## Development Workflow<a name="development-workflow"></a>

You start development by downloading Amazon FreeRTOS\. You unzip the package and import it into your IDE\. You can then develop an application on your selected hardware platform and manufacture and deploy these devices using the development process appropriate for your device\. Deployed devices can connect to the AWS IoT service or AWS IoT Greengrass as part of a complete IoT solution\. The following diagram shows the development workflow and the subsequent connectivity from Amazon FreeRTOS\-based devices\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/workflow.png)

For more information about developing applications with Amazon FreeRTOS, see the [Amazon FreeRTOS Developer Guide](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-dev-guide.html)\.

## Additional Resources<a name="resources"></a>

If you have other questions about AWS or Amazon FreeRTOS, you might find the following resources helpful\.


| Resource | Description | 
| --- | --- | 
| [Amazon FreeRTOS on GitHub](https://github.com/aws/amazon-freertos/issues) | If you have questions about Amazon FreeRTOS for the Amazon FreeRTOS engineering team, you can open an issue on the Amazon FreeRTOS GitHub page\. | 
|  [AWS Discussion Forums](https://forums.aws.amazon.com/)  | To discuss technical questions about AWS and Amazon FreeRTOS with the AWS community, visit the Discussion Forums\. | 
|  [AWS Support Center](https://aws.amazon.com/support) |  To get technical support for AWS, visit the Support Center\.  | 
| [Contact Us](https://aws.amazon.com/contact-us/) |  To contact us about AWS billing, account services, events, abuse, and other issues with AWS, visit the Contact Us page\.  | 
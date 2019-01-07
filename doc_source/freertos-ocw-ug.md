# Amazon FreeRTOS Console<a name="freertos-ocw-ug"></a>

You can use the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) to manage software configurations and download Amazon FreeRTOS software for your device\. The Amazon FreeRTOS software is prequalified on a variety of platforms\. It includes the required hardware drivers, libraries, and example projects to help get you started quickly\. You can choose between predefined configurations or create custom configurations\.

Make sure that you have completed the Getting Started with Amazon FreeRTOS [Prerequisites](https://docs.aws.amazon.com/iot/latest/userguide/freertos-prereqs.html)\.

## Predefined Amazon FreeRTOS Configurations<a name="freertos-predefined-configurations"></a>

Predefined configurations are defined for the prequalified platforms:
+ TI CC3220SF\-LAUNCHXL
+ STM32 IoT Discovery Kit
+ NXP LPC54018 IoT Module
+ Microchip Curiosity PIC32MZEF
+ Infineon XMC4800 IoT Connectivity Kit
+ Renesas RSK\+ for RX65N\-2MB
+ MediaTek MT7697Hx Development Kit
+ Xilinx Avnet MicroZed Industrial IoT Starter Kit
+ FreeRTOS Windows Simulator

The predefined configurations make it possible for you to get started quickly with the supported use cases without thinking about which libraries are required\. To use a predefined configuration, browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), find the configuration you want to use, and then choose **Download**\.

You can also customize a predefined configuration if you want to change the Amazon FreeRTOS version, hardware platform, or libraries of the configuration\. Customizing a predefined configuration creates a new custom configuration and does not overwrite the predefined configuration in the Amazon FreeRTOS console\.

**To create a custom configuration from a predefined configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis next to the predefined configuration that you want to customize, and then choose **Customize**\.

1. On the **Configure Amazon FreeRTOS Software** page, choose the Amazon FreeRTOS version, hardware platform, and libraries, and give the new configuration a name and a description\.

1. At the bottom of the page, choose **Create and download** to create and download the custom configuration\.

## Custom Amazon FreeRTOS Configurations<a name="freertos-custom-configurations"></a>

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

## Quick Connect Workflow<a name="freertos-quick-connect"></a>

The Amazon FreeRTOS console also includes the Quick Connect workflow option for all boards with predefined configurations\. The Quick Connect workflow helps you configure and run Amazon FreeRTOS demo applications for AWS IoT and AWS IoT Greengrass\. To get started, choose the **Predefined configurations** tab, find your board, and then choose **Quick connect** and follow the Quick Connect workflow steps\. 
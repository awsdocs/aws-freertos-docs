# Amazon FreeRTOS Console User Guide<a name="freertos-ocw-ug"></a>

## Managing Amazon FreeRTOS Configurations<a name="freertos-manage-configurations"></a>

You can use the [Amazon FreeRTOS Console](https://console.aws.amazon.com/freertos) to manage software configurations and download Amazon FreeRTOS software for your device\. The Amazon FreeRTOS software is pre qualified on a variety of platforms\. It includes the required hardware drivers, libraries, and example projects to help get you started quickly\. You can choose between predefined configurations or create custom configurations\.

Predefined configurations are defined for the pre qualified platforms:
+ TI CC3220SF\-LAUNCHXL
+ STM32 IoT Discovery Kit
+ NXP LPC54018 IoT Module
+ Microchip Curiosity PIC32MZEF
+ FreeRTOS Windows Simulator

The predefined configurations make it possible for you to get started quickly with the supported use cases without thinking about which libraries are required\. To use a predefined configuration, browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), find the configuration you want to use, and then choose **Download**\.

Custom configurations allow you to specify your hardware platform, integrated development platform \(IDE\), compiler, and only those RTOS libraries you require\. This leaves more space on your devices for application code\.

**To create a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) and choose **Create new**\.

1. On the **New Software Configuration** page, choose **Select a hardware platform**, and choose one of the pre qualified platforms\.

1. Choose the IDE and compiler you want use\.

1. For the Amazon FreeRTOS libraries you require, choose **Add Library**\. If you choose a library that requires another library, it is added for you\. If you want to choose more libraries, choose **Add another library**\.

1. In the **Demo Projects** section, enable one of the demo projects\. This enables the demo in the project files\.

1. In **Name required**, type a name for your custom configuration\.
**Note**  
Do not use any personally identifiable information for your custom configuration name\.

1. In **Description**, you can type a description for your custom configuration\.

1. At the bottom of the page, choose **Create and download** to create and download your custom configuration\.

### Deleting a Configuration<a name="freertos-delete-configuration"></a>

**To delete an Amazon FreeRTOS configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis next to the configuration you want to delete, and then choose **Delete**\.
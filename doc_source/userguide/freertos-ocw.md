# Amazon FreeRTOS Console<a name="freertos-ocw"></a>

In the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), you can download a package with a predefined configuration, or you can create your own configuration by selecting your hardware platform and the libraries required for your application\. These configurations are saved in AWS and are available for download at any time\.

## Predefined Amazon FreeRTOS Configurations<a name="freertos-predefined-configurations"></a>

The predefined configurations make it possible for you to get started quickly with the supported use cases without thinking about which libraries are required\. To use a predefined configuration, browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), find the configuration you want to use, and then choose **Download**\.

You can also customize a predefined configuration if you want to change the Amazon FreeRTOS version, hardware platform, or libraries of the configuration\. Customizing a predefined configuration creates a new custom configuration and does not overwrite the predefined configuration in the Amazon FreeRTOS console\.

**To create a custom configuration from a predefined configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis \(**…**\) next to the predefined configuration that you want to customize, and then choose **Customize**\.

1. On the **Configure Amazon FreeRTOS Software** page, choose the Amazon FreeRTOS version, hardware platform, and libraries, and give the new configuration a name and a description\.

1. At the bottom of the page, choose **Create and download**\.

## Custom Amazon FreeRTOS Configurations<a name="freertos-custom-configurations"></a>

Custom configurations allow you to specify your hardware platform, integrated development platform \(IDE\), compiler, and only those RTOS libraries you require\. This leaves more space on your devices for application code\.

**To create a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) and choose **Create new**\.

1. Select the version of Amazon FreeRTOS that you want to use\. The latest version is used by default\.

1. On the **New Software Configuration** page, choose **Select a hardware platform**, and choose one of the pre\-qualified platforms\.

1. Choose the IDE and compiler you want use\.

1. For the Amazon FreeRTOS libraries you require, choose **Add Library**\. If you choose a library that requires another library, it is added for you\. If you want to choose more libraries, choose **Add another library**\.

1. In the **Demo Projects** section, enable one of the demo projects\. This enables the demo in the project files\.

1. In **Name required**, enter a name for your custom configuration\.
**Note**  
Do not use any personally identifiable information in your custom configuration name\.

1. In **Description**, enter a description for your custom configuration\.

1. At the bottom of the page, choose **Create and download**\.

**To edit a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis \(**…**\) next to the configuration you want to edit, and then choose **Edit**\.

1. On the **Configure Amazon FreeRTOS Software** page, you can change your configuration's Amazon FreeRTOS version, hardware platform, libraries, and description\.

1. At the bottom of the page, choose **Save and download**\.

**To delete a custom configuration**

1. Browse to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose the ellipsis \(**…**\) next to the configuration you want to delete, and then choose **Delete**\.
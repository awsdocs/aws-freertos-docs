# Greengrass Discovery Demo Application<a name="gg-demo"></a>

Before you run the FreeRTOS Greengrass discovery demo, you must create a Greengrass group and then add a Greengrass core\. For more information, see [Getting Started with AWS Greengrass](http://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html)\.

After you have a core running the Greengrass software, create an AWS IoT thing, certificate, and policy for your Amazon FreeRTOS device\. For more information, see [Registering Your MCU Board with AWS IoT](freertos-prereqs.md#get-started-freertos-thing)\.

After you have created an IoT thing for your Amazon FreeRTOS device, follow the instructions to set up your environment and build Amazon FreeRTOS on one of the supported devices:

**Note**  
Use the [Registering Your MCU Board with AWS IoT](freertos-prereqs.md#get-started-freertos-thing) instructions, but instead of downloading one of the predefined Connect to AWS IoT\- XX configurations \(where XX is TI, ST, NXP, Microchip, or Windows\), download one of the Connect to AWS Greengrass \- XX configurations \(where XX is TI, ST, NXP, Microchip, or Windows\)\. Follow the steps in "Configure Your Project\." Return to this topic after you have built Amazon FreeRTOS for your device\.
+ [Getting Started with the Texas Instruments CC3220SF\-LAUNCHXL](getting_started_ti.md)
+ [Getting Started with the STMicroelectronics STM32L4 Discovery Kit IoT Node](getting_started_st.md)
+ [Getting Started with the NXP LPC54018 IoT Module](getting_started_nxp.md)
+ [ Getting Started with the Microchip Curiosity PIC32MZEF](getting_started_mch.md)
+ [Getting Started with the FreeRTOS Windows Simulator](getting_started_windows.md)

At this point, you have downloaded the Amazon FreeRTOS software, imported it into your IDE, and built the project without errors\. The project is already configured to run the Greengrass Connectivity demo\. In the AWS IoT console, choose **Test**, and then add a subscription to `freertos/demos/ggd`\. The demo publishes a series of messages to the Greengrass core\. The messages are also published to AWS IoT, where they are received by the AWS IoT MQTT client\.

In the MQTT client, you should see the following strings:

```
Message from Thing to Greengrass Core: Hello world msg #1!
Message from Thing to Greengrass Core: Hello world msg #0!
Message from Thing to Greengrass Core: Address of Greengrass Core found! <123456789012>.<us-west-2>.compute.amazonaws.com
```
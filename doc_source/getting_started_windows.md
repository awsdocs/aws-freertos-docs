# Getting Started with the Windows Device Simulator<a name="getting_started_windows"></a>

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `BASE_FOLDER`\.

Amazon FreeRTOS is released as a zip file that contains the Amazon FreeRTOS libraries and sample applications for the platform you specify\. To run the samples on a Windows machine, download the libraries and samples ported to run on Windows\. This set of files is referred to as the FreeRTOS simulator for Windows\.

## Setting Up Your Environment<a name="win-setup-env"></a>

1. Install the latest version of [WinPCap](https://www.winpcap.org/)\.

1. Install [Microsoft Visual Studio Community 2017](https://www.visualstudio.com/downloads) with the **Desktop development with C\+\+** workflow and install the Windows 8\.1 SDK\.

1. Make sure that you have an active hard\-wired Ethernet connection\.

**Configure Your Network Interface**

1. Run the project in Visual Studio\. The program enumerates your network interfaces\. Find the number for your hard\-wired Ethernet interface\. The output should look like this:

   ```
   0 0 [None] FreeRTOS_IPInit
   1 0 [None] vTaskStartScheduler
   1. rpcap://\Device\NPF_{AD01B877-A0C1-4F33-8256-EE1F4480B70D}
   (Network adapter 'Intel(R) Ethernet Connection (4) I219-LM' on local host)
   		
   2. rpcap://\Device\NPF_{337F7AF9-2520-4667-8EFF-2B575A98B580}
   (Network adapter 'Microsoft' on local host)
   		
   The interface that will be opened is set by "configNETWORK_INTERFACE_TO_USE" which should be defined in FreeRTOSConfig.h Attempting to open interface number 1.
   ```

   You might see output in the debugger that says **Cannot find or open the PDB file**\. You can ignore these messages\.

   After you have identified the number for your hard\-wired Ethernet interface, close the application window\.

1. Open `<BASE_FOLDER>\demos\pc\windows\common\config_files\FreeRTOSConfig.h` and set `configNETWORK_INTERFACE_TO_USE` to the number that corresponds to your hard\-wired network interface\.

## Build and Run Amazon FreeRTOS Demo Project<a name="win-build-and-run-example"></a>

### Load the Amazon FreeRTOS Demo into Visual Studio<a name="win-load-project"></a>

1. In Visual Studio, from the **File** menu, choose **Open**\. Choose **File/Solution**, navigate to `<BASE_FOLDER>\demos\pc\windows\visual_studio\aws_demos.sln`, and then choose **Open**\.

1. From the **Build** menu, choose **Build Solution**, and make sure the solution builds without errors or warnings\.

### Run the Amazon FreeRTOS Demo<a name="win-run-example"></a>

1. Rebuild your Visual Studio project to pick up changes made in the header files\.

1. From the **Debug** menu in Visual Studio, choose **Start Debugging**\.

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="windows-troubleshooting"></a>

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
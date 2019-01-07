# Getting Started with the FreeRTOS Windows Simulator<a name="getting_started_windows"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

Amazon FreeRTOS is released as a zip file that contains the Amazon FreeRTOS libraries and sample applications for the platform you specify\. To run the samples on a Windows machine, download the libraries and samples ported to run on Windows\. This set of files is referred to as the FreeRTOS simulator for Windows\.

## Setting Up Your Environment<a name="win-setup-env"></a>

1. Install the latest version of [WinPCap](https://www.winpcap.org/)\.

1. Install [Microsoft Visual Studio Community 2017](https://www.visualstudio.com/downloads)\.

1. Make sure that you have an active hard\-wired Ethernet connection\.

## Download and Configure Amazon FreeRTOS<a name="win-download-and-configure"></a>

After your environment is set up, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="win-download"></a><a name="win-download-free-rtos"></a>

1. In the AWS IoT console, browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. In the list of software configurations, find the **Connect to AWS IoT\- Windows** predefined configuration for the Windows simulator, and then choose **Download**\.

1. Unzip the downloaded file to the AmazonFreeRTOS folder, and make a note of the folder's path\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="win-config-project"></a>

#### Configure Your Network Interface<a name="win-config-network"></a>

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

To run the demo, you must configure your project to work with AWS IoT\. To configure your project to work with AWS IoT, your board must be registered as an AWS IoT thing\. This is a step in the [Prerequisites](freertos-prereqs.md)\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint appears in the **Endpoint** text box\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\. Make a note of the AWS IoT thing name for your device\. 

1. With your AWS IoT endpoint and your AWS IoT thing name on hand, open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` in your IDE, and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *Your board's AWS IoT thing name*

**To configure your AWS IoT credentials**

To configure your AWS IoT credentials, you need the private key and certificate that you downloaded from the AWS IoT console when you registered your device as an AWS IoT thing\. After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\. You need to format the certificate and private key for your device\. 

1. In a browser window, open `<BASE_FOLDER>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key should be hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## Build and Run Amazon FreeRTOS Demo Project<a name="win-build-and-run-example"></a>

### Load the Amazon FreeRTOS Demo into Visual Studio<a name="win-load-project"></a>

1. In Visual Studio, from the **File** menu, choose **Open**\. Choose **File/Solution**, navigate to `<BASE_FOLDER>\demos\pc\windows\visual_studio\aws_demos.sln`, and then choose **Open**\.

1. From the **Build** menu, choose **Build Solution**, and make sure the solution builds without errors or warnings\.

### Run the Amazon FreeRTOS Demo<a name="win-run-example"></a>

1. Rebuild your Visual Studio project to pick up changes made in the header files\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. From the **Debug** menu in Visual Studio, choose **Start Debugging**\.

In the [AWS IoT console](https://console.aws.amazon.com/iotv2/), the MQTT client displays the messages received from the FreeRTOS Windows simulator\.
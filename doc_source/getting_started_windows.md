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

1. Unzip the downloaded file to a folder, and make a note of the folder path\. In this tutorial, this folder is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

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

1. Open `AmazonFreeRTOS\demos\pc\windows\common\config_files\FreeRTOSConfig.h` and set `configNETWORK_INTERFACE_TO_USE` to the number that corresponds to your hard\-wired network interface\.

#### Configure Your AWS IoT Endpoint<a name="win-config-endpoint"></a>

You must specify a custom AWS IoT endpoint for the FreeRTOS sample code to connect to AWS IoT\.

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

1. Copy your custom AWS IoT endpoint from the **Endpoint** text box\. It should look like `<c3p0r2d2a1b2c3>.iot.<us-east-1>.amazonaws.com`\.

1. Open `aws_demos/application_code/common_demos/include/aws_clientcredential.h` and set `clientcredentialMQTT_BROKER_ENDPOINT` to your AWS IoT endpoint\.

#### Configure Your AWS IoT Credentials<a name="win-configure-credentials"></a>

The certificate and private key must be hard\-coded into the Amazon FreeRTOS demo code\. This is for demonstration purposes only\. Production\-level applications should store these files in a secure location\. Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

**To format your certificate and private key**

1. In a browser window, open `<BASE_FOLDER>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h** and save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the stub file in the directory\.

## Build and Run Amazon FreeRTOS Samples<a name="win-build-and-run-example"></a>

### Load the Amazon FreeRTOS Sample Code into Visual Studio<a name="win-load-project"></a>

1. In Visual Studio, from the **File** menu, choose **Open**\. Choose **File/Solution**, navigate to `<BASE_FOLDER>\AmazonFreeRTOS\demos\pc\windows\visual_studio\aws_demos.sln`, and then choose **Open**\.

1. From the **Build** menu, choose **Build Solution**, and make sure the solution builds without errors or warnings\.

### Run the Amazon FreeRTOS Samples<a name="win-run-example"></a>

1. Rebuild your Visual Studio project to pick up changes made in the header files\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. From the **Debug** menu in Visual Studio, choose **Start Debugging**\.

In the [AWS IoT console](https://console.aws.amazon.com/iotv2/), the MQTT client displays the messages received from the FreeRTOS Windows simulator\.
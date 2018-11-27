# Getting Started with the Infineon XMC4800 IoT Connectivity Kit<a name="getting_started_infineon"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the Infineon XMC4800 IoT Connectivity Kit, you can purchase one from [Infineon](https://www.infineon.com/cms/en/product/evaluation-boards/kit_xmc48_iot_aws_wifi/)\.

If you want to open a serial connection with the board to view logging and debugging information, you need a 3\.3V USB/Serial converter, in addition to the XMC4800 IoT Connectivity Kit\. The CP2104 is a common USB/Serial converter that is widely available in boards such as Adafruit's [CP2104 Friend](https://www.adafruit.com/product/3309)\.

## Setting Up Your Environment<a name="infineon-setup-env"></a>

Amazon FreeRTOS uses Infineon's DAVE development environment to program the XMC4800\. Before you begin, you need to download and install DAVE and some J\-Link drivers to communicate with the on\-board debugger\.

### Install DAVE<a name="install-dave"></a>

1. Go to Infineon's [DAVE software download](https://infineoncommunity.com/dave-download_ID645) page\.

1. Choose the DAVE package for your operating system and submit your registration information\. After registering with Infineon, you should receive a confirmation email with a link to download a \.zip file\.

1. Download the DAVE package \.zip file \(`DAVE_version_os_date.zip`\), and unzip it to the location where you want to install DAVE \(for example, `C:\DAVE4`\)\. 
**Note**  
Some Windows users have reported problems using Windows Explorer to unzip the file\. We recommend that you use a third\-party program such as 7\-Zip\.

1. To launch DAVE, run the executable file found in the unzipped `DAVE_version_os_date.zip` folder\.

For more information, see the [DAVE Quick Start Guide](https://www.infineon.com/dgdl/Infineon-DAVE_Quick_Start-GS-v02_00-EN.pdf?fileId=5546d4624cb7f111014d059f7b8c712d)\.

### Install Segger J\-Link Drivers<a name="install-jlink"></a>

To communicate with the XMC4800 Relax EtherCAT board's on\-board debugging probe, you need the drivers included in the J\-Link Software and Documentation pack\. You can download the J\-Link Software and Documentation pack from Segger's [J\-Link software download](https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack) page\.

### Set Up a Serial Connection<a name="install-serial-connection"></a>

Setting up a serial connection is optional, but recommended\. A serial connection allows your board to send logging and debugging information in a form that you can view on your development machine\.

The XMC4800 demo application uses a UART serial connection on pins P0\.0 and P0\.1, which are labeled on the XMC4800 Relax EtherCAT board's silkscreen\. To set up a serial connection:

1. Connect the pin labeled “RX<P0\.0” to your USB/Serial converter's “TX” pin\.

1. Connect the pin labeled “TX>P0\.1” to your USB/Serial converter's “RX” pin\.

1. Connect your serial converter's Ground pin to one of the pins labeled “GND” on your board\. The devices must share a common ground\.

Power is supplied from the USB debugging port, so do not connect your serial adapter's positive voltage pin to the board\.

**Note**  
Some serial cables use a 5V signaling level\. The XMC4800 board and the Wi\-Fi Click module require a 3\.3V\. Do not use the board's IOREF jumper to change the board's signals to 5V\.

 With the cable connected, you can open a serial connection on a terminal emulator such as [GNU Screen](https://www.gnu.org/software/screen/)\. The baud rate is set to 115200 by default with 8 data bits, no parity, and 1 stop bit\. 

## Download and Configure Amazon FreeRTOS<a name="infineon-download-and-configure"></a>

After you set up your environment, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="infineon-download"></a><a name="infineon-download-free-rtos"></a>

1. Browse to the AWS IoT console\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Under **Software Configurations**, find **Connect to AWS IoT\- Infineon**, and then choose **Download**\.

1. Unzip the downloaded file to the AmazonFreeRTOS folder, and make note of the folder path\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="infineon-freertos-config-project"></a>

To run the demo, you must configure your project to work with AWS IoT\. To configure your project to work with AWS IoT, your board must be registered as an AWS IoT thing\. [Registering Your MCU Board with AWS IoT](freertos-prereqs.md#get-started-freertos-thing) is a step in the [Prerequisites](freertos-prereqs.md)\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint is displayed in **Endpoint**\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

   Your device should have an AWS IoT thing name\. Make a note of this name\.

1. In your IDE, open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *The AWS IoT thing name of your board*

**To configure your Wi\-Fi**

1. Open the `aws_clientcredential.h` file\.

1. Specify values for the following `#define` constants:
   + `clientcredentialWIFI_SSID` *The SSID for your Wi\-Fi network*
   + `clientcredentialWIFI_PASSWORD` *The password for your Wi\-Fi network*
   + `clientcredentialWIFI_SECURITY` *The security type of your Wi\-Fi network*

     Valid security types are:
     + `eWiFiSecurityOpen` \(Open, no security\)
     + `eWiFiSecurityWEP` \(WEP security\)
     + `eWiFiSecurityWPA` \(WPA security\)
     + `eWiFiSecurityWPA2` \(WPA2 security\)

**To configure your AWS IoT credentials**
**Note**  
To configure your AWS IoT credentials, you need the private key and certificate that you downloaded from the AWS IoT console when you registered your device\. After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\. You must format the certificate and private key for your device\.

1. In a browser window, open `<BASE_FOLDER>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## Build and Run Amazon FreeRTOS Samples<a name="infineon-build-and-run-example"></a>

### Import the Amazon FreeRTOS Sample Code into DAVE<a name="infineon-freertos-import-project"></a><a name="infineon-load-project"></a>

1. Start DAVE\.

1. In DAVE, choose **File**, **Import**\. In the **Import** window, expand the **Infineon** folder, choose **DAVE Project**, and then choose **Next**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/startdave1.png)

1. In the **Import DAVE Projects** window, choose **Select Root Directory**, choose **Browse**, and then choose the XMC4800 demo project\.

   In the directory where you unzipped your Amazon FreeRTOS download, the demo project is located in `<BASE_FOLDER>/demos/infineon/xmc4800_iotkit/dave`\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/startdave2.png)

   Make sure that **Copy Projects Into Workspace** is unchecked\.

1. Choose **Finish**\.

   The `aws_demos` project should be imported into your workspace and activated\.

1. From the **Project** menu, choose **Build Active Project**\.

   Make sure that the project builds without errors\.

### Run the FreeRTOS Demo<a name="infineon-run-examples"></a>

After you have configured your project, you are ready to run the demo project on your board\.

1. Use a USB cable to connect your XMC4800 IoT Connectivity Kit to your computer\. The board has two microUSB connectors\. Use the one labeled “X101”, where Debug appears next to it on the board's silkscreen\.

1. From the **Project** menu, choose **Rebuild Active Project** to rebuild `aws_demos` and ensure that your configuration changes are picked up\.

1. Sign in to the AWS IoT console\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. From **Project Explorer**, right\-click `aws_demos`, choose **Debug As**, and then choose **DAVE C/C\+\+ Application**\.

1. Double\-click **GDB SEGGER J\-Link Debugging** to create a debug confirmation\. Choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

In the AWS IoT console, the MQTT client from steps 4\-5 should display the MQTT messages sent by your device\. If you use the serial connection, you see something like this on the UART output:

```
0 0 [Tmr Svc] Starting key provisioning...
1 1 [Tmr Svc] Write root certificate...
2 4 [Tmr Svc] Write device private key...
3 82 [Tmr Svc] Write device certificate...
4 86 [Tmr Svc] Key provisioning done...
5 291 [Tmr Svc] Wi-Fi module initialized. Connecting to AP...
.6 8046 [Tmr Svc] Wi-Fi Connected to AP. Creating tasks which use network...
7 8058 [Tmr Svc] IP Address acquired [IP Address]
8 8058 [Tmr Svc] Creating MQTT Echo Task...
9 8059 [MQTTEcho] MQTT echo attempting to connect to [MQTT Broker].
...10 23010 [MQTTEcho] MQTT echo connected.
11 23010 [MQTTEcho] MQTT echo test echoing task created.
.12 26011 [MQTTEcho] MQTT Echo demo subscribed to freertos/demos/echo
13 29012 [MQTTEcho] Echo successfully published 'Hello World 0'
.14 32096 [Echoing] Message returned with ACK: 'Hello World 0 ACK'
.15 37013 [MQTTEcho] Echo successfully published 'Hello World 1'
16 40080 [Echoing] Message returned with ACK: 'Hello World 1 ACK'
.17 45014 [MQTTEcho] Echo successfully published 'Hello World 2'
.18 48091 [Echoing] Message returned with ACK: 'Hello World 2 ACK'
.19 53015 [MQTTEcho] Echo successfully published 'Hello World 3'
.20 56087 [Echoing] Message returned with ACK: 'Hello World 3 ACK'
.21 61016 [MQTTEcho] Echo successfully published 'Hello World 4'
22 64083 [Echoing] Message returned with ACK: 'Hello World 4 ACK'
.23 69017 [MQTTEcho] Echo successfully published 'Hello World 5'
.24 72091 [Echoing] Message returned with ACK: 'Hello World 5 ACK'
.25 77018 [MQTTEcho] Echo successfully published 'Hello World 6'
26 80085 [Echoing] Message returned with ACK: 'Hello World 6 ACK'
.27 85019 [MQTTEcho] Echo successfully published 'Hello World 7'
.28 88086 [Echoing] Message returned with ACK: 'Hello World 7 ACK'
.29 93020 [MQTTEcho] Echo successfully published 'Hello World 8'
.30 96088 [Echoing] Message returned with ACK: 'Hello World 8 ACK'
.31 101021 [MQTTEcho] Echo successfully published 'Hello World 9'
32 104102 [Echoing] Message returned with ACK: 'Hello World 9 ACK'
.33 109022 [MQTTEcho] Echo successfully published 'Hello World 10'
.34 112047 [Echoing] Message returned with ACK: 'Hello World 10 ACK'
.35 117023 [MQTTEcho] Echo successfully published 'Hello World 11'
36 120089 [Echoing] Message returned with ACK: 'Hello World 11 ACK'
.37 122068 [MQTTEcho] MQTT echo demo finished.
38 122068 [MQTTEcho] ----Demo finished----
```
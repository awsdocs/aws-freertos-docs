# Getting Started with the MediaTek MT7697Hx Development Kit<a name="getting_started_mediatek"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the MediaTek MT7697Hx Development Kit, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AAOmPUAX/MT7697Hx-Development-Kit)\.

## Setting Up Your Environment<a name="mediatek-setup-env"></a>

Before you set up your environment, connect your computer to the USB port on the MediaTek MT7697Hx Development Kit\.

### Download and Install Keil MDK<a name="install-keil-mdk"></a>

You can use the GUI\-based Keil Microcontroller Development Kit \(MDK\) to configure, build, and run Amazon FreeRTOS projects on your board\. Keil MDK includes the μVision IDE and the μVision Debugger\.

**Note**  
Keil MDK is supported on Windows 7, Windows 8, and Windows 10 64\-bit machines only\.

**To download and install Keil MDK**

1. Go to the [Keil MDK Getting Started](http://www2.keil.com/mdk5/install/) page, and choose **Download MDK\-Core**\.

1. Enter and submit your information to register with Keil\.

1. Right\-click the MDK executable and save the Keil MDK installer to your computer\.

1. Open the Keil MDK installer and follow the steps to completion\. Make sure that you install the MediaTek device pack \(MT76x7 Series\)\.

### Set Up a Serial Connection<a name="mediatek-serial-connection"></a>

To establish a serial connection with the MediaTek MT7697Hx Development Kit, you must install the Arm Mbed Windows serial port driver\. You can download the driver from [Mbed](https://os.mbed.com/docs/latest/tutorials/windows-serial-driver.html)\. Follow the steps on the **Windows serial driver** page to download and install the driver for the MediaTek MT7697Hx Development Kit\. 

After you install the driver, a COM port appears in the Windows Device Manager\. For debugging, you can open a session to the port with a terminal utility tool such as HyperTerminal or TeraTerm\.

## Download and Configure Amazon FreeRTOS<a name="mediatek-download-and-configure"></a>

After you set up your environment, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="mediatek-download"></a><a name="mediatek-download-free-rtos"></a>

**To download Amazon FreeRTOS**

1. Browse to the AWS IoT console\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Under **Software Configurations**, find **Connect to AWS IoT \- MediaTek**, and then choose **Download**\.

1. Unzip the downloaded file to a folder, and make a note of the folder path\. The unzipped folder is named `AmazonFreeRTOS`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="mediatek-freertos-config-project"></a>

To run the demo, you must configure your project to work with AWS IoT, which requires that you register your board as an AWS IoT thing\. [Registering Your MCU Board with AWS IoT](freertos-prereqs.md#get-started-freertos-thing) is a step in the [Prerequisites](freertos-prereqs.md)\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint is displayed in **Endpoint**\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

   Your device should have an AWS IoT thing name\. Make a note of this name\.

1. In your IDE, open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *The AWS IoT thing name of your board*

**To configure your Wi\-Fi settings**

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

## Build and Run the Amazon FreeRTOS Demo Project with Keil MDK<a name="mediatek-build-and-run-keil"></a>

**To build the Amazon FreeRTOS demo project in Keil μVision**

1. From the **Start** menu, open Keil μVision 5\.

1. Open the `<BASE_FOLDER>/demos/mediatek/mt7697hx-dev-kit/uvision/aws_demo.uvprojx` project file\.

1. From the menu, choose **Project**, and then choose **Build target**\.

   After the code is built, you see the demo executable file at `<BASE_FOLDER>/demos/mediatek/mt7697hx-dev-kit/uvision/out/Objects/aws_demo.axf`\.

**To run the Amazon FreeRTOS demo project**

1. Set the MediaTek MT7697Hx Development Kit to PROGRAM mode\.

   To set the kit to PROGRAM mode, press and hold the **PROG** button\. With the **PROG** button still pressed, press and release the **RESET** button, and then release the **PROG** button\.

1. From the menu, choose **Flash**, and then choose **Configure Flash Tools**\.

1. In **Options for Target '`aws_demo`'**, choose the **Debug** tab\. Select **Use**, set the debugger to **CMSIS\-DAP Debugger**, and then choose **OK**\.

1. From the menu, choose **Flash**, and then choose **Download**\.

   μVision notifies you when the download is complete\.

1. Use a terminal utility to open the serial console window\. Set the serial port to 115200 bps, none\-parity, 8\-bits, and 1 stop\-bit\.

1. Choose the **RESET** button on your MediaTek MT7697Hx Development Kit\.

## Debugging Amazon FreeRTOS Projects in Keil μVision<a name="mediatek-debugging"></a>

Currently, you must edit the MediaTek package that is included with Keil μVision before you can debug the Amazon FreeRTOS demo project for MediaTek with Keil μVision\.

**To edit the MediaTek package for debugging Amazon FreeRTOS projects**

1. Find and open the `Keil_v5\ARM\PACK\MediaTek\MTx\4.6.1\MediaTek.MTx.pdsc` file in your Keil MDK installation folder\.

1. Replace all instances of `flag = Read32(0x20000000);` with `flag = Read32(0x0010FBFC);`\.

1. Replace all instances of `Write32(0x20000000, 0x76877697);` with `Write32(0x0010FBFC, 0x76877697);`\.

**To start debugging the project**

1. From the menu, choose **Flash**, and then choose **Configure Flash Tools**\.

1. Choose the **Target** tab, and then choose **Read/Write Memory Areas**\. Confirm that IRAM1 and IRAM2 are both selected\.

1. Choose the **Debug** tab, and then choose **CMSIS\-DAP Debugger**\.

1. Open `<BASE_FOLDER>/demos/mediatek/mt7697hx-dev-kit/common/application_code/main.c`, and set the macro `MTK_DEBUGGER` to `1`\. 

1. Rebuild the demo project in μVision\. 

1. Set the MediaTek MT7697Hx Development Kit to PROGRAM mode\.

   To set the kit to PROGRAM mode, press and hold the **PROG** button\. With the **PROG** button still pressed, press and release the **RESET** button, and then release the **PROG** button\.

1. From the μVision menu, choose **Debug**, and then choose **Start/Stop Debug Session**\. The **Call Stack \+ Locals** window opens when you start the debug session\. μVision flashes the demo to the board, runs the demo, and stops at the beginning of the `main()` function\. 

1. From the menu, choose **Debug**, and then choose **Stop** to stop the session\. The program counter stops at the following line: 

   ```
   { volatile int wait_ice = 1 ; while ( wait_ice ) ; }
   ```

1. In the **Call Stack \+ Locals** window, change the value for `wait_ice` to `0`\. 

1. Set breakpoints in your project's source code, and run the code\. 
# Getting Started with the Microchip Curiosity PIC32MZEF<a name="getting_started_mch"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the Microchip Curiosity PIC32MZEF bundle, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANscUAH/Curiosity-PIC32MZ-EF-Amazon-FreeRTOS-Bundle)\. You need the following items:
+ [MikroElectronika USB UART Click Board](https://www.mikroe.com/usb-uart-click)
+ [RJ\-11 to ICSP Adapter](https://www.microchipdirect.com/product/search/all/ac164110)
+ [MPLAB ICD 4 In\-Circuit Debugger](http://www.microchip.com/Developmenttools/ProductDetails.aspx?PartNO=DV164045)
+ [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3)
+ [MikroElectronika WiFi 7 Click Board](https://www.mikroe.com/wifi-7-click) 

## Setting Up the Microchip Curiosity PIC32MZEF Hardware<a name="setup-hw-mch"></a>

1. Connect the [MikroElectronika USB UART Click Board](https://download.mikroe.com/documents/add-on-boards/click/usb-uart/usb-uart-click-manual-v100.pdf) to the microBUS 1 connector on the Microchip Curiosity PIC32MZEF\.

1. Connect the [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3) to the J18 header on the Microchip Curiosity PIC32MZEF\.

1. Connect the [MikroElectronika USB UART Click Board](https://www.mikroe.com/usb-uart-click) to your computer using a USB A to USB mini\-B cable\.

1. Connect the [MikroElectronika WiFi 7 Click Board](https://www.mikroe.com/wifi-7-click) to the microBUS 2 connector on the Microchip Curiosity PIC32MZEF\.

1. Connect the [RJ\-11 to ICSP Adapter](https://www.microchipdirect.com/product/search/all/ac164110) to the Microchip Curiosity PIC32MZEF\.

1. Connect the MPLAB ICD 4 In\-Circuit Debugger to your Microchip Curiosity PIC32MZEF using an RJ\-11 cable\.

1. Connect the ICD 4 In\-Circuit Debugger to your computer using a USB A to USB mini\-B cable\.

1. Insert the RJ\-11 to ICSP Adaptor J2 into the ICSP header on the Microchip Curiosity PIC32MZEF at the J16\.

1. Connect one end of an Ethernet cable to the LAN8720 PHY daughter board\. Connect the other end to your router or other internet port\.

The following image shows the Microchip Curiosity PIC32MZEF and all required peripherals assembled\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/microchip-setup.png)

The LED on in\-circuit debugger turns a solid blue when it is ready\.

## Setting Up Your Environment<a name="setup-env_mch"></a>

1. Install the latest [Java SE SDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)\.

1. Install [Python version 3\.x](http://www.python.org/downloads) or later\.

1. Install the latest version of the MPLAB X IDE:
   + [MPLAB X Integrated Development Environment for Windows](http://www.microchip.com/mplabx-ide-windows-installer)
   + [MPLAB X Integrated Development Environment for macOS](http://www.microchip.com/mplabx-ide-osx-installer)
   + [MPLAB X Integrated Development Environment for Linux](http://www.microchip.com/mplabx-ide-linux-installer)

1. Install the latest version of the MPLAB XC32 Compiler:
   + [MPLAB XC32/32\+\+ Compiler for Windows](http://www.microchip.com/mplabxc32windows)
   + [MPLAB XC32/32\+\+ Compiler for macOS](http://www.microchip.com/mplabxc32osx)
   + [MPLAB XC32/32\+\+ Compiler for Linux](http://www.microchip.com/mplabxc32linux)

1. Install the latest version of the MPLAB Harmony Integrated Software Framework \(optional\):
   + [MPLAB Harmony Integrated Software Framework for Windows](http://www.microchip.com/mymicrochip/filehandler.aspx?ddocname=en603881)
   + [MPLAB Harmony Integrated Software Framework for macOS](http://www.microchip.com/mymicrochip/filehandler.aspx?ddocname=en603883)
   + [MPLAB Harmony Integrated Software Framework for Linux](http://www.microchip.com/mymicrochip/filehandler.aspx?ddocname=en603882)

1. Start up a UART terminal emulator and open a connection with the following settings:
   + Baud rate: 115200
   + Data: 8 bit
   + Parity: None
   + Stop bits: 1
   + Flow control: None

## Download and Configure Amazon FreeRTOS<a name="mch-download-and-configure"></a>

After your environment is set up, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="mch-download"></a><a name="mch-download-free-rtos"></a>

1. Browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos) in the AWS IoT console\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. In **Software Configurations**, find **Connect to AWS IoT\- Microchip**, and then choose **Download**\.

1. Unzip the downloaded file to the AmazonFreeRTOS folder, and make a note of the folder's path\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="mch-freertos-config-project"></a>

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

## Build and Run Amazon FreeRTOS Demo Project<a name="mch-build-and-run-example"></a>

### Open the Amazon FreeRTOS Demo in the MPLAB IDE<a name="mch-freertos-import-project"></a><a name="mch-load-project"></a>

1. In the MPLAB IDE, from the **File** menu, choose **Open Project**\.

1. Browse to and open `<BASE_FOLDER>\demos\microchip\curiosity_pic32mzef\mplab`\.

1. Choose **Open project**\.

**Note**  
When you open the project for the first time, you can ignore warning messages like the following:  

```
warning: Configuration "pic32mz_ef_curiosity" builds with "XC32", but indicates no toolchain directory.
warning: Configuration "pic32mz_ef_curiosity" refers to file "AmazonFreeRTOS/lib/third_party/mcu_vendor/microchip/harmony/framework/bootloader/src/bootloader.h" which does not exist in the disk. The make process might not build correctly.
```

### Run the Amazon FreeRTOS Demo Project<a name="mch-run-example"></a>

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. On the **Projects** tab, right\-click the `aws_demos` top\-level folder, and then choose **Debug**\.

1. The first time you debug the samples, an **ICD 4 not Found** dialog box is displayed\. In the tree view, under the **ICD 4** node, choose the ICD4 serial number, and then choose **OK**\.

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

The ICD 4 turns half yellow as it is programming the device, and then half green when it is running\. The **ICD4** tab appears in the IDE\. Successful programming looks like the following: 

```
*****************************************************

 
Connecting to MPLAB ICD 4...

Currently loaded versions:
Application version............01.02.00
Boot version...................01.00.00
FPGA version...................01.00.00
Script version.................00.02.18
Script build number............fd44437f19
Application build number.......0123456789

Connecting to MPLAB ICD 4...

Currently loaded versions:
Boot version...................01.00.00
Updating firmware application...
Connecting to MPLAB ICD 4...

Currently loaded versions:
Application version............01.02.16
Boot version...................01.00.00
FPGA version...................01.00.00
Script version.................00.02.18
Script build number............fd44437f19
Application build number.......0123456789

Target voltage detected
Target device PIC32MZ2048EFM100 found.
Device Id Revision = 0xA1
Serial Number:
Num0 = ec4f6d3c
Num1 = 6b845410

Erasing...

The following memory area(s) will be programmed:
program memory: start address = 0x1d000000, end address = 0x1d07bfff
program memory: start address = 0x1d1fc000, end address = 0x1d1fffff
configuration memory
boot config memory

Programming/Verify complete

Running
```

**Note**  
We recommend that you use the MPLAB In\-Circuit Debugger instead of the USB port for debugging\. The ICD 4 makes it possible for you to step through code more quickly and add breakpoints without restarting the debugger\.

You should see MQTT messages sent by your device in the MQTT client of the AWS IoT console\.

## Troubleshooting<a name="getting_started_mch_troubleshooting"></a>

If no messages appear in the AWS IoT console, try the following:

1. Open a terminal window to view the logging output of the sample\. This can help you determine what is going wrong\.

1. Check that your network credentials are valid\.
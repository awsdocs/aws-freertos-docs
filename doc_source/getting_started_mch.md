# Getting Started with the Microchip Curiosity PIC32MZEF<a name="getting_started_mch"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the Microchip Curiosity PIC32MZEF bundle, you can purchase one from [Microchip](http://www.microchipdirect.com/product/search/all/dm320104-BNDL)\. You need the following items:
+ [MikroElectronika USB UART Click Board](https://www.mikroe.com/usb-uart-click)
+ [RJ\-11 to ICSP Adapter](https://www.microchipdirect.com/product/search/all/ac164110)
+ [MPLAB ICD 4 In\-Circuit Debugger](http://www.microchip.com/Developmenttools/ProductDetails.aspx?PartNO=DV164045)
+ [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3)
+ [MikroElectronika WiFi 7 Click Board](https://www.mikroe.com/wifi-7-click) 

## Setting Up the Microchip Curiosity PIC32MZEF Hardware<a name="setup-hw-mch"></a>

1. Connect the [MikroElectronika USB UART Click Board](https://download.mikroe.com/documents/add-on-boards/click/usb-uart/usb-uart-click-manual-v100.pdf) to the microBUS 1 connector on the Microchip Curiosity PIC32MZEF\.

1. Connect the [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3) to the J18 header on the Microchip Curiosity PIC32MZEF\.

1. Connect the [MikroElectronika USB UART Click Board](https://www.mikroe.com/usb-uart-click) to your computer using a USB A to USB Mini B cable\.

1. Connect the [MikroElectronika WiFi 7 Click Board](https://www.mikroe.com/wifi-7-click) to the microBUS 2 connector on the Microchip Curiosity PIC32MZEF\.

1. Connect the [RJ\-11 to ICSP Adapter](https://www.microchipdirect.com/product/search/all/ac164110) to the Microchip Curiosity PIC32MZEF\.

1. Connect the MPLAB ICD 4 In\-Circuit Debugger to your Microchip Curiosity PIC32MZEF using an RJ\-11 cable\.

1. Connect the ICD 4 In\-Circuit Debugger to your computer using a USB A to USB mini\-B cable\.

1. Insert the RJ\-11 to ICSP Adaptor J2 into the ICSP header on the Microchip Curiosity PIC32MZEF at the J16\.

1. Connect one end of an Ethernet cable to the LAN8720 PHY daughter board\. Connect the other end of the Ethernet cable to your router or other internet port\.

The following image shows the Microchip Curiosity PIC32MZEF and all required peripherals assembled\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/microchip-setup.png)

The LED on in\-circuit debugger will turn on a solid blue when it is ready\.

## Setting Up Your Environment<a name="setup-env_mch"></a>

1. Install the latest [Java SE SDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)\.

1. Install the latest version of the MPLAB X Integrated Development Environment:
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

## Download and Build Amazon FreeRTOS<a name="mch-download-and-build"></a>

After your environment is set up, you can download Amazon FreeRTOS and run the sample code\.

### Download Amazon FreeRTOS<a name="mch-download"></a><a name="mch-download-free-rtos"></a>

1. Browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos) in the AWS IoT console\.

1. In the left navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. In **Software Configurations**, find **Connect to AWS IoT\- Microchip**, and then choose **Download**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/download-treadstone-microchip.png)

1. Unzip the downloaded file to a folder, and make a note of the folder path\. In this tutorial, this folder is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

### Open the Amazon FreeRTOS Demo Application in the MPLAB IDE<a name="mch-freertos-import-project"></a><a name="mch-load-project"></a>

1. In the MPLAB IDE, from the **File** menu, choose **Open Project**\.

1. Browse to and open `<BASE_FOLDER>\AmazonFreeRTOS\demos\microchip\curiosity_pic32mzef\mplab`\.

1. Choose **Open project** to import the project\.

**Note**  
You may see some warning messages when opening the project for the first time\. These messages may look like the following:  

```
warning: Configuration "pic32mz_ef_curiosity" builds with "XC32", but indicates no toolchain directory.
warning: Configuration "pic32mz_ef_curiosity" refers to file "AmazonFreeRTOS/lib/third_party/mcu_vendor/microchip/harmony/framework/bootloader/src/bootloader.h" which does not exist in the disk. The make process might not build correctly.
```
These warnings may be ignored\.

### Configure Your Project<a name="mch-config-project"></a>

To configure your project, you need to know your AWS IoT endpoint\.

**To find your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Settings**\.

1. Copy your AWS IoT endpoint from the **Endpoint** text box\. It should look like `<1234567890123>.iot.<us-east-1>.amazonaws.com`\.

You also need to know your Wi\-FI network SSID, password, and security type and the name of the AWS IoT thing that represents your device\.

In the **Project Explorer** window, open `aws_demos\application_code\common_demos\include\aws_clientcredential.h`\.

Specify values for the following `#define` constants:
+ `clientcredentialMQTT_BROKER_ENDPOINT`: Your AWS IoT endpoint\.
+ `clientcredentialIOT_THING_NAME`: The AWS IoT thing for your board\.
+ `clientcredentialWIFI_SSID`: The SSID for your Wi\-Fi network\.
+ `clientcredentialWIFI_PASSWORD`: The password for your Wi\-Fi network\.
+ `clientcredentialWIFI_SECURITY`: The security type for your Wi\-Fi network\. Valid security types are:
  + `eWiFiSecurityOpen`: Open, no security\.
  + `eWiFiSecurityWEP`: WEP security\.
  + `eWiFiSecurityWPA`: WPA security\.
  + `eWiFiSecurityWPA2`: WPA2 security\.

Make sure to save your changes\.

#### Configure Your AWS IoT Credentials<a name="mch-configure-credentials"></a>

The certificate and private key must be hard\-coded into the Amazon FreeRTOS demo code\. This is for demo purposes only\. Production level applications should store these files in a secure location\. Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

**To format your certificate and private key**

1. In a browser window, open `<BASE_FOLDER>\demos\common\devmode_key_provisioning\CertificateConfigurationTool\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the stub file in the directory\.

### Run the FreeRTOS Samples<a name="mch-run-example"></a>

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Test** to open the MQTT client\.

1. In the **Subscription topic** text box, type **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. On the **Projects** tab, right\-click the aws\_demos top\-level folder and then choose **Debug**\.

1. The first time you debug the samples, an **ICD 4 not Found** dialog box appears\. In the tree view, under the **ICD 4** node, choose the ICD4 serial number, and then choose **OK**\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Run** menu, and then choose **Resume**\.

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

In the MQTT client in the AWS IoT console, you should see the MQTT messages sent by your device\.

### Troubleshooting<a name="getting_started_mch_troubleshooting"></a>

If no messages appear in the AWS IoT console, try the following:

1. Open a terminal window to view the logging output of the sample\. This can help you determine what is going wrong\.

1. Check that your network credentials are valid\.
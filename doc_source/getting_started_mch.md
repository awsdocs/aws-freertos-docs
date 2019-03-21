# Getting Started with the Microchip Curiosity PIC32MZEF<a name="getting_started_mch"></a>

If you do not have the Microchip Curiosity PIC32MZEF bundle, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANscUAH/Curiosity-PIC32MZ-EF-Amazon-FreeRTOS-Bundle)\. You need the following items:
+ [MikroElectronika USB UART Click Board](https://www.mikroe.com/usb-uart-click)
+ [RJ\-11 to ICSP Adapter](https://www.microchipdirect.com/product/search/all/ac164110)
+ [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3)
+ [MikroElectronika WiFi 7 Click Board](https://www.mikroe.com/wifi-7-click) 

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `BASE_FOLDER`\.

## Setting Up the Microchip Curiosity PIC32MZEF Hardware<a name="setup-hw-mch"></a>

1. Connect the [MikroElectronika USB UART Click Board](https://download.mikroe.com/documents/add-on-boards/click/usb-uart/usb-uart-click-manual-v100.pdf) to the microBUS 1 connector on the Microchip Curiosity PIC32MZEF\.

1. Connect the [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3) to the J18 header on the Microchip Curiosity PIC32MZEF\.

1. Connect the [MikroElectronika USB UART Click Board](https://www.mikroe.com/usb-uart-click) to your computer using a USB A to USB mini\-B cable\.

1. Connect the [MikroElectronika WiFi 7 Click Board](https://www.mikroe.com/wifi-7-click) to the microBUS 2 connector on the Microchip Curiosity PIC32MZEF\.

1. Connect the [RJ\-11 to ICSP Adapter](https://www.microchipdirect.com/product/search/all/ac164110) to the Microchip Curiosity PIC32MZEF\.

1. Insert the RJ\-11 to ICSP Adaptor J2 into the ICSP header on the Microchip Curiosity PIC32MZEF at the J16\.

1. Connect one end of an Ethernet cable to the LAN8720 PHY daughter board\. Connect the other end to your router or other internet port\.

## Setting Up Your Environment<a name="setup-env_mch"></a>

1. Install the latest [Java SE SDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)\.

1. Install [Python version 3\.x](https://www.python.org/downloads/) or later\.

1. Install the latest version of the MPLAB X IDE:
   + [MPLAB X Integrated Development Environment for Windows](http://www.microchip.com/mplabx-ide-windows-installer)
   + [MPLAB X Integrated Development Environment for macOS](http://www.microchip.com/mplabx-ide-osx-installer)
   + [MPLAB X Integrated Development Environment for Linux](http://www.microchip.com/mplabx-ide-linux-installer)

1. Install version 2\.10 of the MPLAB XC32 Compiler:
   + [MPLAB XC32/32\+\+ Compiler for Windows](http://ww1.microchip.com/downloads/en/DeviceDoc/xc32-v2.10-full-install-windows-installer.exe)
   + [MPLAB XC32/32\+\+ Compiler for macOS](http://ww1.microchip.com/downloads/en/DeviceDoc/xc32-v2.10-full-install-osx-installer.dmg)
   + [MPLAB XC32/32\+\+ Compiler for Linux](http://ww1.microchip.com/downloads/en/DeviceDoc/xc32-v2.10-full-install-linux-installer.run)

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

1. On the **Projects** tab, right\-click the `aws_demos` top\-level folder, and then choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

**Note**  
For improved performance, you can optionally purchase the [MPLAB ICD 4 In\-Circuit Debugger](http://www.microchip.com/Developmenttools/ProductDetails.aspx?PartNO=DV164045)\. The ICD 4 makes it possible for you to step through code more quickly, add an unlimited number of breakpoints, and add breakpoints without restarting the debugger\.  
The first time you debug the samples with the ICD 4 debugger, an **ICD 4 not Found** dialog box is displayed\. In the tree view, under the **ICD 4** node, choose the ICD4 serial number, and then choose **OK**\.

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="getting_started_mch_troubleshooting"></a>

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
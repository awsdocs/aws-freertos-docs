# Getting Started with the Microchip Curiosity PIC32MZ EF<a name="getting_started_mch"></a>

This tutorial provides instructions for getting started with the Microchip Curiosity PIC32MZ EF\. If you do not have the Microchip Curiosity PIC32MZ EF bundle, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANscUAH/Curiosity-PIC32MZ-EF-Amazon-FreeRTOS-Bundle)\.

The bundle includes the following items:
+ [Curiosity PIC32MZ EF Development Board](https://www.microchip.com/Developmenttools/ProductDetails/DM320104)
+ [MikroElectronika USB UART click Board](https://www.mikroe.com/usb-uart-click)
+ [MikroElectronika WiFi 7 click Board](https://www.mikroe.com/wifi-7-click) 
+ [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3)

You also need the following items for debugging:
+ [MPLAB Snap In\-Circuit Debugger](https://www.microchip.com/Developmenttools/ProductDetails/PG164100) 
+ \(Optional\) [PICkit 3 Programming Cable Kit](https://new.microchipdirect.com/product/search/all/TPROG001) 

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `BASE_FOLDER`\.

## Overview<a name="w3aab7c19c19c15"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling an Amazon FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Set Up the Microchip Curiosity PIC32MZ EF Hardware<a name="setup-hw-mch"></a>

1. Connect the MikroElectronika USB UART click Board to the microBUS 1 connector on the Microchip Curiosity PIC32MZ EF\.

1. Connect the PIC32 LAN8720 PHY daughter board to the J18 header on the Microchip Curiosity PIC32MZ EF\.

1. Connect the MikroElectronika USB UART click Board to your computer using a USB A to USB mini\-B cable\.

1. Connect the MikroElectronika WiFi 7 click Board to the microBUS 2 connector on the Microchip Curiosity PIC32MZ EF\.

1. If not done already, solder the angle connector to the ICSP header on the Microchip Curiosity PIC32MZ EF\.

1. Connect one end of the ICSP cable from the PICkit 3 Programming Cable Kit to the Microchip Curiosity PIC32MZ EF\.

   If you don't have the PICkit 3 Programming Cable Kit, you can use M\-F Dupont wire jumpers to wire the connection instead\. Note that the white circle signifies the position of Pin 1\.

1. Connect the other end of the ICSP cable \(or jumpers\) to the MPLAB Snap Debugger\. Pin 1 of the 8\-pin SIL Programming Connector is marked by the black triangle on the bottom right of the board\.

   Make sure that any cabling to Pin 1 on the Microchip Curiosity PIC32MZ EF, signified by the white circle, aligns with Pin 1 on the MPLAB Snap Debugger\.

   For more information about the MPLAB Snap Debugger, see the [MPLAB Snap In\-Circuit Debugger Information Sheet](http://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20Snap%20In-Circuit%20Debugger%20IS%20DS50002787A.pdf)\.

1. Connect one end of an Ethernet cable to the LAN8720 PHY daughter board\. Connect the other end to your router or other internet port\.

## Set Up Your Development Environment<a name="setup-env_mch"></a>

1. Install the latest [Java SE SDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)\.

1. Install [Python version 3\.x](https://www.python.org/downloads/) or later\.

1. Install the latest version of the MPLAB X IDE:
   + [MPLAB X Integrated Development Environment for Windows](http://www.microchip.com/mplabx-ide-windows-installer)
   + [MPLAB X Integrated Development Environment for macOS](http://www.microchip.com/mplabx-ide-osx-installer)
   + [MPLAB X Integrated Development Environment for Linux](http://www.microchip.com/mplabx-ide-linux-installer)

1. Install the latest version of the MPLAB XC32 Compiler:
   + [MPLAB XC32/32\+\+ Compiler for Windows](http://www.microchip.com/mplabxc32windows)
   + [MPLAB XC32/32\+\+ Compiler for macOS](http://www.microchip.com/mplabxc32osx)
   + [MPLAB XC32/32\+\+ Compiler for Linux](http://www.microchip.com/mplabxc32linux)

1. \(Optional\) Install the latest version of the MPLAB Harmony Integrated Software Framework:
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

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="getting_started_mch_troubleshooting"></a>

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
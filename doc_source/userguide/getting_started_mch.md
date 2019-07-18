# Getting Started with the Microchip Curiosity PIC32MZ EF<a name="getting_started_mch"></a>

This tutorial provides instructions for getting started with the Microchip Curiosity PIC32MZ EF\. If you do not have the Microchip Curiosity PIC32MZ EF bundle, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANscUAH/Curiosity-PIC32MZ-EF-Amazon-FreeRTOS-Bundle)\.

The bundle includes the following items:
+ [Curiosity PIC32MZ EF Development Board](https://www.microchip.com/Developmenttools/ProductDetails/DM320104)
+ [MikroElectronika USB UART click Board](https://www.mikroe.com/usb-uart-click)
+ [MikroElectronika WiFi 7 click Board](https://www.mikroe.com/wifi-7-click) 
+ [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3)

You also need the following items for debugging:
+ [MPLAB Snap In\-Circuit Debugger](https://www.microchip.com/Developmenttools/ProductDetails/PG164100) 
+ \(Optional\) [PICkit 3 Programming Cable Kit](https://www.microchip.com/TPROG001) 

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `<amazon-freertos>`\.

## Overview<a name="w3aab7c23c23c15"></a>

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

**Note**  
The Amazon FreeRTOS project for this device is based on MPLAB Harmony v2\. To build the project, you need to use versions of the MPLAB tools that are compatible with Harmony v2, like v2\.10 of the MPLAB XC32 Compiler and versions 2\.X\.X of the MPLAB Harmony Configurator \(MHC\)\.

1. Install [Python version 3\.x](https://www.python.org/downloads/) or later\.

1. Install the MPLAB X IDE:
   + [MPLAB X Integrated Development Environment for Windows](http://www.microchip.com/mplabx-ide-windows-installer)
   + [MPLAB X Integrated Development Environment for macOS](http://www.microchip.com/mplabx-ide-osx-installer)
   + [MPLAB X Integrated Development Environment for Linux](http://www.microchip.com/mplabx-ide-linux-installer)

1. Install the MPLAB XC32 Compiler:
   + [MPLAB XC32/32\+\+ Compiler for Windows](http://www.microchip.com/mplabxc32windows)
   + [MPLAB XC32/32\+\+ Compiler for macOS](http://www.microchip.com/mplabxc32osx)
   + [MPLAB XC32/32\+\+ Compiler for Linux](http://www.microchip.com/mplabxc32linux)

1. Start up a UART terminal emulator and open a connection with the following settings:
   + Baud rate: 115200
   + Data: 8 bit
   + Parity: None
   + Stop bits: 1
   + Flow control: None

## Build and Run Amazon FreeRTOS Demo Project<a name="mch-build-and-run-example"></a>

### Open the Amazon FreeRTOS Demo in the MPLAB IDE<a name="mch-freertos-import-project"></a><a name="mch-load-project"></a>

1. Open MPLAB IDE\. If you have more than one version of the compiler installed, you need to select the compiler that you want to use from within the IDE\. 

1. From the **File** menu, choose **Open Project**\.

1. Browse to and open `projects/microchip/curiosity_pic32mzef/mplab/aws_demos`\.

1. Choose **Open project**\.

**Note**  
When you open the project for the first time, you might get an error message about the compiler\. In the IDE, navigate to **Tools**, **Options**, **Embedded**, and then select the compiler that you are using for your project\.

### Run the Amazon FreeRTOS Demo Project<a name="mch-run-example"></a>

1. Rebuild your project\.

1. On the **Projects** tab, right\-click the `aws_demos` top\-level folder, and then choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

### Build the Amazon FreeRTOS demo with CMake<a name="microchip-cmake"></a>

If you prefer not to use an IDE for Amazon FreeRTOS development, you can alternatively use CMake to build and run the demo applications or applications that you have developed using third\-party code editors and debugging tools\.

**To build the Amazon FreeRTOS demo with CMake**

1. Create a folder to contain the generated build files \(*BUILD\_FOLDER*\)\.

1. Use the following command to generate build files from source code:

   ```
   cmake -DVENDOR=microchip -DBOARD=curiosity_pic32mzef -DCOMPILER=xc32 -DMCHP_HEXMATE_PATH=path/microchip/mplabx/v5.10/mplab_platform/bin  -DAFR_TOOLCHAIN_PATH=path/microchip/xc32/v2.15/bin -S <amazon-freertos> -B BUILD_FOLDER -DAFR_ENABLE_TESTS=1
   ```
**Note**  
You must specify the correct paths to the Hexmate and toolchain binaries\.

1. Change directories to the build directory \(*BUILD\_FOLDER*\), and run `make` from that directory\.

For more information, see [Using CMake with Amazon FreeRTOS](getting-started-cmake.md)\.

### Monitoring MQTT Messages on the Cloud<a name="w3aab7c23c23c21b9"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="getting_started_mch_troubleshooting"></a>

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
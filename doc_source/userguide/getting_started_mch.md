# Getting started with the Microchip Curiosity PIC32MZ EF<a name="getting_started_mch"></a>

**Note**  
In agreement with Microchip, we are removing the Curiosity PIC32MZEF \(DM320104\) from the FreeRTOS Reference Integration repository main branch and will no longer carry it in new releases\. Microchip has issued an [official notice](https://www.microchip.com/DevelopmentTools/ProductDetails/PartNO/DM320104) that the PIC32MZEF \(DM320104\) is no longer recommended for new designs\. The PIC32MZEF projects and source code can still be accessed through the previous release tags\. Microchip recommends that customers use the Curiosity [ PIC32MZ\-EF\-2\.0 Development board \(DM320209\)](https://devices.amazonaws.com/detail/a3G0h0000077I69EAE/Curiosity-PIC32MZ-EF-2-0-Development-Board) for new designs\. The PIC32MZv1 platform can still be found in [v202012\.00](https://github.com/aws/amazon-freertos/tree/202012.00) of the FreeRTOS Reference Integration repository\. However, the platform is no longer supported by [v202107\.00](https://github.com/aws/amazon-freertos/tree/202107.00) of the FreeRTOS Reference\.

This tutorial provides instructions for getting started with the Microchip Curiosity PIC32MZ EF\. If you do not have the Microchip Curiosity PIC32MZ EF bundle, visit the AWS Partner Device Catalog to purchase one from our [ partner](https://devices.amazonaws.com/detail/a3G0L00000AANscUAH/Curiosity-PIC32MZ-EF-Amazon-FreeRTOS-Bundle)\.

The bundle includes the following items:
+ [Curiosity PIC32MZ EF Development Board](https://www.microchip.com/Developmenttools/ProductDetails/DM320104)
+ [MikroElectronika USB UART click Board](https://www.mikroe.com/usb-uart-click)
+ [MikroElectronika WiFi 7 click Board](https://www.mikroe.com/wifi-7-click) 
+ [PIC32 LAN8720 PHY daughter board](http://www.microchip.com/DevelopmentTools/ProductDetails.aspx?PartNO=ac320004-3)

You also need the following items for debugging:
+ [MPLAB Snap In\-Circuit Debugger](https://www.microchip.com/Developmenttools/ProductDetails/PG164100)
+ \(Optional\) [PICkit 3 Programming Cable Kit](https://www.microchip.com/TPROG001)

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. See [First steps](freertos-prereqs.md) for instructions\.

**Important**  
In this topic, the path to the FreeRTOS download directory is referred to as `freertos`\.
Space characters in the `freertos` path can cause build failures\. When you clone or copy the repository, make sure the path that you create doesn't contain space characters\.
The maximum length of a file path on Microsoft Windows is 260 characters\. Long FreeRTOS download directory paths can cause build failures\.
Because the source code may contain symbolic links, if you're using Windows to extract the archive, you may have to:  
Enable [ Developer Mode](https://docs.microsoft.com/en-us/windows/apps/get-started/enable-your-device-for-development) or, 
Use a console that is elevated as administrator\.
In this way, Windows can properly create symbolic links when it extracts the archive\. Otherwise, symbolic links will be written as normal files that contain the paths of the symbolic links as text or are empty\. For more information, see the blog entry [ Symlinks in Windows 10\!](https://blogs.windows.com/windowsdeveloper/2016/12/02/symlinks-windows-10/)\.  
If you use Git under Windows, you must enable Developer Mode or you must:   
Set `core.symlinks` to true with the following command:  

    ```
    git config --global core.symlinks true
    ```
Use a console that is elevated as administrator whenever you use a git command that writes to the system \(for example, git pull, git clone, and git submodule update \-\-init \-\-recursive\)\.

## Overview<a name="getting_started_mch_overview"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Set up the Microchip Curiosity PIC32MZ EF hardware<a name="setup-hw-mch"></a>

1. Connect the MikroElectronika USB UART click Board to the microBUS 1 connector on the Microchip Curiosity PIC32MZ EF\.

1. Connect the PIC32 LAN8720 PHY daughter board to the J18 header on the Microchip Curiosity PIC32MZ EF\.

1. Connect the MikroElectronika USB UART click Board to your computer using a USB A to USB mini\-B cable\.

1. To connect your board to the internet, use one of the following options:
   + To use **Wi\-Fi**, connect the MikroElectronika Wi\-Fi 7 click Board to the microBUS 2 connector on the Microchip Curiosity PIC32MZ EF\. See [Configuring the FreeRTOS demos](freertos-prereqs.md#freertos-configure)\.
   + To use **Ethernet** to connect the Microchip Curiosity PIC32MZ EF Board to the internet, connect the PIC32 LAN8720 PHY daughter board to the J18 header on the Microchip Curiosity PIC32MZ EF\. Connect one end of an Ethernet cable to the LAN8720 PHY daughter board\. Connect the other end to your router or other internet port\. You must also define the preprocessor macro `PIC32_USE_ETHERNET`\.

1. If not done already, solder the angle connector to the ICSP header on the Microchip Curiosity PIC32MZ EF\.

1. Connect one end of the ICSP cable from the PICkit 3 Programming Cable Kit to the Microchip Curiosity PIC32MZ EF\.

   If you don't have the PICkit 3 Programming Cable Kit, you can use M\-F Dupont wire jumpers to wire the connection instead\. Note that the white circle signifies the position of Pin 1\.

1. Connect the other end of the ICSP cable \(or jumpers\) to the MPLAB Snap Debugger\. Pin 1 of the 8\-pin SIL Programming Connector is marked by the black triangle on the bottom right of the board\.

   Make sure that any cabling to Pin 1 on the Microchip Curiosity PIC32MZ EF, signified by the white circle, aligns with Pin 1 on the MPLAB Snap Debugger\.

   For more information about the MPLAB Snap Debugger, see the [ MPLAB Snap In\-Circuit Debugger Information Sheet](http://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20Snap%20In-Circuit%20Debugger%20IS%20DS50002787A.pdf)\.

## Set up the Microchip Curiosity PIC32MZ EF hardware using PICkit On Board \(PKOB\)<a name="setup-env_mch-pkob"></a>

We recommend that you follow the setup procedure in the previous section\. However, you can evaluate and run FreeRTOS demos with basic debugging using the integrated PICkit On Board \(PKOB\) programmer/debugger by following these steps\.

1. Connect the MikroElectronika USB UART click Board to the microBUS 1 connector on the Microchip Curiosity PIC32MZ EF\.

1. To connect your board to the internet, do one of the following:
   + To use **Wi\-Fi**, connect the MikroElectronika Wi\-Fi 7 click Board to the microBUS 2 connector on the Microchip Curiosity PIC32MZ EF\. \(Follow the steps "To configure your Wi\-Fi" in [Configuring the FreeRTOS demos](freertos-prereqs.md#freertos-configure)\.
   + To use **Ethernet** to connect the Microchip Curiosity PIC32MZ EF Board to the internet, connect the PIC32 LAN8720 PHY daughter board to the J18 header on the Microchip Curiosity PIC32MZ EF\. Connect one end of an Ethernet cable to the LAN8720 PHY daughter board\. Connect the other end to your router or other internet port\. You must also define the preprocessor macro `PIC32_USE_ETHERNET`\.

1. Connect the USB micro\-B port named "USB DEBUG" on the Microchip Curiosity PIC32MZ EF Board to your computer using a USB type A to USB micro\-B cable\.

1. Connect the MikroElectronika USB UART click Board to your computer using a USB A to USB mini\-B cable\.

## Set up your development environment<a name="setup-env_mch"></a>

**Note**  
The FreeRTOS project for this device is based on MPLAB Harmony v2\. To build the project, you need to use versions of the MPLAB tools that are compatible with Harmony v2, like v2\.10 of the MPLAB XC32 Compiler and versions 2\.X\.X of the MPLAB Harmony Configurator \(MHC\)\.

1. Install [Python version 3\.x](https://www.python.org/downloads/) or later\.

1. Install the MPLAB X IDE:
**Note**  
FreeRTOS AWS Reference Integrations v202007\.00 is currently supported on MPLabv5\.35 only\. Prior versions of FreeRTOS AWS Reference Integrations are supported on MPLabv5\.40\.

**MPLabv5\.35 downloads**
   + [ MPLAB X Integrated Development Environment for Windows](http://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v5.35-windows-installer.exe)
   + [ MPLAB X Integrated Development Environment for macOS](http://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v5.35-osx-installer.dmg)
   + [ MPLAB X Integrated Development Environment for Linux](http://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v5.35-linux-installer.tar)

**Latest MPLab downloads \(MPLabv5\.40\)**
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

## Monitoring MQTT messages in the cloud<a name="getting_started_mch_monitor_mqtt"></a>

Before you run the FreeRTOS demo project, you can set up the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test**, then choose **MQTT test client** to open the MQTT client\.

1. In **Subscription topic**, enter ***your\-thing\-name*/example/topic**, and then choose **Subscribe to topic**\.

When the demo project successfully runs on your device you see "Hello World\!" sent multiple times to the topic that you subscribed to\.

## Build and run the FreeRTOS demo project<a name="mch-build-and-run-example"></a>

### Open the FreeRTOS demo in the MPLAB IDE<a name="mch-freertos-import-project"></a><a name="mch-load-project"></a>

1. Open MPLAB IDE\. If you have more than one version of the compiler installed, you need to select the compiler that you want to use from within the IDE\. 

1. From the **File** menu, choose **Open Project**\.

1. Browse to and open `projects/microchip/curiosity_pic32mzef/mplab/aws_demos`\.

1. Choose **Open project**\.

**Note**  
When you open the project for the first time, you might get an error message about the compiler\. In the IDE, navigate to **Tools**, **Options**, **Embedded**, and then select the compiler that you are using for your project\.

To use Ethernet to connect, you must define the preprocessor macro `PIC32_USE_ETHERNET`\. 

**To use Ethernet to connect using the MPLAB IDE**

1. In the MPLAB IDE, right\-click the project and choose **Properties**\.

1. In the **Project Properties** dialog box, choose ***compiler\-name* \(Global Options\)** to expand it, and then select ***compiler\-name*\-gcc**\.

1. For **Options categories**, choose **Preprocessing and messages**, and then add the `PIC32_USE_ETHERNET` string to **Preprocessor macros**\.

### Run the FreeRTOS demo project<a name="mch-run-example"></a>

1. Rebuild your project\.

1. On the **Projects** tab, right\-click the `aws_demos` top\-level folder, and then choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

### Build the FreeRTOS demo with CMake<a name="microchip-cmake"></a>

If you prefer not to use an IDE for FreeRTOS development, you can alternatively use CMake to build and run the demo applications or applications that you have developed using third\-party code editors and debugging tools\.

**To build the FreeRTOS demo with CMake**

1. Create a directory to contain the generated build files, such as *build\-directory*\.

1. Use the following command to generate build files from source code\.

   ```
   cmake -DVENDOR=microchip -DBOARD=curiosity_pic32mzef -DCOMPILER=xc32 -DMCHP_HEXMATE_PATH=path/microchip/mplabx/version/mplab_platform/bin  -DAFR_TOOLCHAIN_PATH=path/microchip/xc32/version/bin -S freertos -B build-folder
   ```
**Note**  
You must specify the correct paths to the Hexmate and toolchain binaries, such as the `C:\Program Files (x86)\Microchip\MPLABX\v5.35\mplab_platform\bin` and `C:\Program Files\Microchip\xc32\v2.40\bin` paths\.

1. Change directories to the build directory \(*build\-directory*\), and then run `make` from that directory\.

For more information, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

To use Ethernet to connect, you must define the preprocessor macro `PIC32_USE_ETHERNET`\. 

## Troubleshooting<a name="getting_started_mch_troubleshooting"></a>

For troubleshooting information, see [Troubleshooting getting started](gsg-troubleshooting.md)\.
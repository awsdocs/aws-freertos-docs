# Getting Started with the Infineon OPTIGA™ Trust X and XMC4800 IoT Connectivity Kit<a name="getting_started_infineon_trust_x"></a>

This tutorial provides instructions for getting started with the Infineon OPTIGA™ Trust X Secure Element and XMC4800 IoT Connectivity Kit\. In comparison to the [Getting Started with the Infineon XMC4800 IoT Connectivity Kit](getting_started_infineon.md) tutorial, this guide shows you how to provide secure credentials using an Infineon OPTIGA™ Trust X Secure Element\.

The following hardware is required for this tutorial:

1. Host MCU \- Infineon XMC4800 IoT Connectivity Kit, visit the AWS Partner Device Catalog to purchase one from our [ partner](https://devices.amazonaws.com/detail/a3G0L00000AANsbUAH/XMC4800-IoT-Amazon-FreeRTOS-Connectivity-Kit-WiFi)\.

1. Security Extension Pack: 
   + Secure Element \- Infineon OPTIGA™ Trust X\.

     Visit the AWS Partner Device Catalog to purchase them from our [ partner](https://devices.amazonaws.com/detail/a3G0h000000TePnEAK/OPTIGA%E2%84%A2-Trust-X-Security-Solution)\.
   + Personalization Board \- Infineon OPTIGA™ Personalisation Board\.
   + Adapter Board \- Infineon MyIoT Adapter\.

To follow the steps here, you must open a serial connection with the board to view logging and debugging information\. \(One of the steps requires you to copy a public key from the serial debugging output from the board and paste it to a file\.\) To do this, you need a 3\.3V USB/Serial converter in addition to the XMC4800 IoT Connectivity Kit\. The [ JBtek EL\-PN\-47310126](https://www.amazon.com/gp/product/B00QT7LQ88) USB/Serial converter is known to work for this demo\. You will also need three male\-to\-male [jumper wires](https://www.amazon.com/gp/product/B077N6HFCX/) \(for receive \(RX\), transmit \(TX\) and ground \(GND\)\) to connect the serial cable to the Infineon MyIoT Adapter board\.  

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [Option \#2: Onboard Private Key Generation](dev-mode-key-provisioning.md#dev-mode-key-provisioning-option2) in *Developer\-Mode Key Provisioning* for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `<amazon-freertos>`\.

## Overview<a name="getting_started_infineon_trust_x_overview"></a>

This tutorial contains instructions for the following getting started steps:

1. Install software on the host machine to develop and debug embedded applications for your microcontroller board\.

1. Cross compile an Amazon FreeRTOS demo application to a binary image\.

1. Load the application binary image to your board, then run the application\.

1. Interact with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Set Up Your Development Environment<a name="infineon_trust_x_setup_env"></a>

Amazon FreeRTOS uses Infineon's DAVE development environment to program the XMC4800\. Before you begin, you need to download and install DAVE and some J\-Link drivers to communicate with the on\-board debugger\.

### Install DAVE<a name="infineon_trust_x_install_dave"></a>

1. Go to Infineon's [DAVE software download](https://infineoncommunity.com/dave-download_ID645) page\.

1. Choose the DAVE package for your operating system and submit your registration information\. After registering with Infineon, you should receive a confirmation email with a link to download a \.zip file\.

1. Download the DAVE package \.zip file \(`DAVE_version_os_date.zip`\), and unzip it to the location where you want to install DAVE \(for example, `C:\DAVE4`\)\. 
**Note**  
Some Windows users have reported problems using Windows Explorer to unzip the file\. We recommend that you use a third\-party program such as 7\-Zip\.

1. To launch DAVE, run the executable file found in the unzipped `DAVE_version_os_date.zip` folder\.

For more information, see the [ DAVE Quick Start Guide](https://www.infineon.com/dgdl/Infineon-DAVE_Quick_Start-GS-v02_00-EN.pdf?fileId=5546d4624cb7f111014d059f7b8c712d)\.

### Install Segger J\-Link Drivers<a name="infineon_trust_x_install_jlink"></a>

To communicate with the XMC4800 IoT Connectivity kit's on\-board debugging probe, you need the drivers included in the J\-Link Software and Documentation pack\. You can download the J\-Link Software and Documentation pack from Segger's [J\-Link software download](https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack) page\.

## Establish a Serial Connection<a name="infineon_trust_x_install_serial_connection"></a>

Connect the USB/Serial converter cable to the Infineon Shield2Go Adapter\. This allows your board to send logging and debugging information in a form that you can view on your development machine\.  To set up a serial connection:

1. Connect the pin labeled “RX” to your USB/Serial converter's "TX" pin\.

1. Connect the pin labeled "TX" to your USB/Serial converter's "RX" pin\.

1. Connect your serial converter's Ground pin to one of the pins labeled "GND" on your board\. The devices must share a common ground\.

Power is supplied from the USB debugging port, so do not connect your serial adapter's positive voltage pin to the board\.

**Note**  
Some serial cables use a 5V signaling level\. The XMC4800 board and the Wi\-Fi Click module require a 3\.3V\. Do not use the board's IOREF jumper to change the board's signals to 5V\.

 With the cable connected, you can open a serial connection on a terminal emulator such as [GNU Screen](https://www.gnu.org/software/screen/)\. The baud rate is set to 115200 by default with 8 data bits, no parity, and 1 stop bit\. 

## Monitoring MQTT Messages on the Cloud<a name="infineon_trust_x_monitoring"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\. You may want to set this up now, before the device runs the demo project\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="infineon_trust_x_build_and_run_example"></a>

### Import the Amazon FreeRTOS Demo into DAVE<a name="infineon_trust_x_freertos_import-project"></a><a name="infineon_trust_x_load_project"></a>

1. Start DAVE\.

1. In DAVE, choose **File**, **Import**\. In the **Import** window, expand the **Infineon** folder, choose **DAVE Project**, and then choose **Next**\.

1. In the **Import DAVE Projects** window, choose **Select Root Directory**, choose **Browse**, and then choose the XMC4800 demo project\.

   In the directory where you unzipped your Amazon FreeRTOS download, the demo project is located in `projects/infineon/xmc4800_plus_optiga_trust_x/dave4/aws_demos/dave4`\.

   Make sure that **Copy Projects Into Workspace** is unchecked\.

1. Choose **Finish**\.

   The `aws_demos` project should be imported into your workspace and activated\.

1. From the **Project** menu, choose **Build Active Project**\. 

   Make sure that the project builds without errors\.

### Run the Amazon FreeRTOS Demo Project<a name="infineon_trust_x_run_examples"></a>

1. From the **Project** menu, choose **Rebuild Active Project** to rebuild `aws_demos` and ensure that your configuration changes are picked up\.

1. From **Project Explorer**, right\-click `aws_demos`, choose **Debug As**, and then choose **DAVE C/C\+\+ Application**\.

1. Double\-click **GDB SEGGER J\-Link Debugging** to create a debug confirmation\. Choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

At this point, continue with the step *Public Key Extraction* given in [Option \#2: Onboard Private Key Generation](dev-mode-key-provisioning.md#dev-mode-key-provisioning-option2)\. Once all the steps are complete, look at the AWS IoT console where the MQTT client you set up previously should display the MQTT messages sent by your device\. Also, via the device's serial connection, you should see something like this on the UART output:

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
.12 26011 [MQTTEcho] MQTT Echo demo subscribed to iotdemo/#
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

#### Build the Amazon FreeRTOS demo with CMake<a name="infineon_trust_x_cmake"></a>

If you prefer not to use an IDE for Amazon FreeRTOS development, you can alternatively use CMake to build and run the demo applications or applications that you have developed using third\-party code editors and debugging tools\.

**Note**  
This section covers using CMake on Windows with MingW as the native build system\. For more information about using CMake with other operating systems and options, see [Using CMake with Amazon FreeRTOS](getting-started-cmake.md)\.

**To build the Amazon FreeRTOS demo with CMake**

1. Set up the GNU Arm Embedded Toolchain\.

   1. Download a Windows version of the toolchain from the [ Arm Embedded Toolchain download page](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)\.
**Note**  
We recommend that you download a version other than "8\-2018\-q4\-major", due to [a bug reported](https://bugs.launchpad.net/gcc-arm-embedded/+bug/1810274) with the "objcopy" utility in that version\.

   1. Open the downloaded toolchain installer, and follow the installation wizard's instructions to install the toolchain\.
**Important**  
On the final page of the installation wizard, select **Add path to environment variable** to add the toolchain path to the system path environment variable\.

1. Install CMake and MingW\.

   For instructions, see [CMake Prerequisites](building-cmake-prereqs.md)\.

1. Create a folder to contain the generated build files \(*BUILD\_FOLDER*\)\. 

1. Change directories to your Amazon FreeRTOS download directory \(`<amazon-freertos>`\), and use the following command to generate the build files:

   ```
   cmake -DVENDOR=infineon -DBOARD=xmc4800_plus_optiga_trust_x -DCOMPILER=arm-gcc -S . -B BUILD_FOLDER -G "MinGW Makefiles" -DAFR_ENABLE_TESTS=0
   ```

1. Change directories to the build directory \(*BUILD\_FOLDER*\), and use the following command to build the binary:

   ```
   cmake --build . --parallel 8
   ```

   This command builds the output binary `aws_demos.hex` to the build directory\.

1. Flash and run the image with [JLINK](getting_started_infineon.md#install-jlink)\.

   1. From the build directory \(*BUILD\_FOLDER*\), use the following commands to create a flash script:

      ```
      echo loadfile aws_demos.hex > flash.jlink
      echo r >> flash.jlink
      echo g >> flash.jlink
      echo q >> flash.jlink
      ```

   1. Flash the image using the JLNIK executable\.

      ```
      JLINK_PATH\JLink.exe  -device XMC4800-2048 -if SWD -speed auto -CommanderScript flash.jlink
      ```

      The application logs should be visible through [ the serial connection](getting_started_infineon.md#install-serial-connection) that you established with the board\. Continue with the step *Public Key Extraction* given in [Option \#2: Onboard Private Key Generation](dev-mode-key-provisioning.md#dev-mode-key-provisioning-option2)\. Once all the steps are complete, look at the AWS IoT console where the MQTT client you set up previously should display the MQTT messages sent by your device\. 

### Troubleshooting<a name="infineon_trust_x_troubleshooting"></a>

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
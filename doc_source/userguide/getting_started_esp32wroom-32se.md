# Getting started with the Espressif ESP32\-WROOM\-32SE \(preview\)<a name="getting_started_esp32wroom-32se"></a>

Follow this tutorial to get started with the Espressif ESP32\-WROOM\-32SE\. Support for the ESP32\-WROOM\-32SE \(with Microchip ATECC608A secure element\) is in preview only and isn't part of the official FreeRTOS release\. The ESP32\-WROOM\-32SE currently has limited availability for purchase\. Contact [sales@espressif\.com](mailto:sales@espressif.com) to obtain a board\.

**Note**  
FreeRTOS port for ESP32\-WROOM\-32SE doesn't support the following features:  
Symmetric multiprocessing \(SMP\)
Online Configuration Wizard \(OCW\)

## Overview<a name="getting_started_esp32wroom-32se-overview"></a>

This tutorial guides you through the following steps:

1. Connect your board to a host machine\.

1. Install software on your host machine to develop and debug embedded applications for your microcontroller board\.

1. Cross\-compile a FreeRTOS demo application to a binary image\.

1. Load the application binary image to your board, and then run the application\.

1. Monitor and debug the running application using a serial connection\.

## Prerequisites<a name="setup-esp32wroom-32se-prereqs"></a>

Before you get started with FreeRTOS on your Espressif board, you need to set up your AWS account and permissions\.

To create an AWS account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an IAM user to your AWS account, see the [Adding a User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) in the *IAM User Guide*\. To grant your IAM user permission to AWS IoT and FreeRTOS, attach the following IAM managed policies to your IAM users:
+ `AmazonFreeRTOSFullAccess`

  Allows full access to all of your IAM user's FreeRTOS resources \(`freertos:*`\)\. 
+ `AWSIoTFullAccess`

  Allows full access to all of your IAM user's IoT resources \(`iot:*`\)\.

**To attach the `AmazonFreeRTOSFullAccess` policy to your IAM user**

1. Navigate to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose ** Users**

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To attach the AWSIoTFullAccess policy to your IAM user**

1. Navigate to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM, see the [ IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide)\.

For more information about policies, see [IAM Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

## Set up the Espressif hardware<a name="setup-hw-espressif-esp32wroom-32se"></a>

For information about setting up the ESP32\-WROOM\-32SE development board hardware, see the [ ESP32\-DevKitC Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-devkitc.html)\.

**Note**  
Don't follow the **Get Started** section of the Espressif guides\. Instead, follow the steps below\.

## Set up your development environment<a name="setup-env-esp32wroom-32se"></a>

To communicate with your board, you need to download and install a toolchain\.

### Set up the toolchain<a name="setup-esp32wroom-32se-toolchain"></a>

To set up the toolchain, follow the instructions for your host machine's operating system:
+ [ Standard Setup of Toolchain for Windows](https://docs.espressif.com/projects/esp-idf/en/v3.3/get-started-cmake/windows-setup.html)
+ [ Standard Setup of Toolchain for macOS](https://docs.espressif.com/projects/esp-idf/en/v3.3/get-started-cmake/macos-setup.html)
+ [ Standard Setup of Toolchain for Linux](https://docs.espressif.com/projects/esp-idf/en/v3.3/get-started-cmake/linux-setup.html)

**Important**  
When you reach the "Get ESP\-IDF" instructions under **Next Steps**, stop and return to the instructions on this page\.

Make sure that the `IDF_PATH` environment variable is cleared from your system before you continue\. This environment variable is automatically set if you followed the "Get ESP\-IDF" instructions under **Next Steps**\.

**Note**  
Version 3\.3 of the ESP\-IDF \(the version that FreeRTOS uses\) doesn't support the latest version of the ESP32 compiler\. You must use the compiler that is compatible with version 3\.3 of the ESP\-IDF\. See the previous links\. To check the version of your compiler, run the following command\.  

```
xtensa-esp32-elf-gcc --version
```

### Install CMake<a name="gsg-esp32wroom-32se-cmake"></a>

The CMake build system is required to build the FreeRTOS demo and test applications for this device\. FreeRTOS supports versions 3\.13 and later\.

You can download the latest version of CMake from [ CMake\.org](https://cmake.org/download/)\. Source and binary distributions are available\.

For more details about using CMake with FreeRTOS, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

## Establish a serial connection<a name="esp32wroom-32se-establish-serial-connection"></a>

1. To establish a serial connection between your host machine and the ESP32\-WROOM\-32SE, install the CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [ Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\.

1. Follow the steps to [ Establish a Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html)\.

1. After you establish a serial connection, make a note of the serial port for your board's connection\. You need it when you build the demo\.

## Download and configure FreeRTOS<a name="download-and-configure-esp32wroom-32se"></a>

After you set up your environment, you can download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. The code for ESP32\-WROOM32\-SE is only available on GitHub on the development branch [ feature/esp32\-wroom\-32se](https://github.com/aws/amazon-freertos/tree/feature/esp32-wroom-32se)\.

**Important**  
The ATECC608A device has a one time initialization that is locked onto the device the first time a project is run \(during the call to `C_InitToken`\)\. However, the FreeRTOS demo project and test project have different configurations\. If the device is locked during the demo project configurations, not all tests in the test project will succeed\.

1. Configure the FreeRTOS Demo Project by following the steps in [Configuring the FreeRTOS demos](freertos-configure.md)\. Skip the last step **To format your AWS IoT credentials** and follow the steps below instead\.

1. Microchip has provided several scripting tools to help with the setup of the ATECC608A parts\. Navigate to the `freertos/vendors/microchip/secure_elements/app/example_trust_chain_tool` directory, and open the `README.md` file\.

   Follow the instructions in the `README.md` file to provision your device\. The steps include:

   1. Create and register a certificate authority with AWS\.

   1. Generate your keys on the ATECC608A and export the public key and device serial number\.

   1. Generate a certificate for the device and register that certificate with AWS\.

1. Load the CA certificate and device certificate onto the device by following the instructions for [Developer\-mode key provisioning](dev-mode-key-provisioning.md)\.

## Build, flash, and run the FreeRTOS demo project<a name="build-and-run-example-esp32wroom-32se"></a>

You can use CMake to generate the build files, Make to build the application binary, and Espressif's IDF utility to flash your board\.

### Build FreeRTOS<a name="build-esp32wroom-32se"></a>

If you're using Windows, you can skip to [Build FreeRTOS on Windows](#build-esp32wroom-32se-windows)\.

Use CMake to generate the build files, and then use Make to build the application\.

**To generate the demo application's build files with CMake**

1. Navigate to the root of your FreeRTOS download directory\.

1. In a command line window, enter the following command to generate the build files\.

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_plus_ecc608a_devkitc -DCOMPILER=xtensa-esp32 -S . -B your-build-directory
   ```
**Note**  
To build the application for debugging, add the `-DCMAKE_BUILD_TYPE=Debug` flag\.  
To generate the test application build files, add the `-DAFR_ENABLE_TESTS=1` flag\.  
To add Lightweight IP \(LwIP\) support, add the `-DAFR_ESP_LWIP=1` flag\.

**To build the application with Make**

1. Navigate to the `build` directory\.

1. In a command line window, enter the following command to build the application with Make\.

   ```
   make all -j4
   ```
**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

### Build FreeRTOS on Windows<a name="build-esp32wroom-32se-windows"></a>

On Windows, you must specify a build generator for CMake\. Otherwise, CMake defaults to Visual Studio\. Espressif officially recommends the Ninja build system because it works on Windows, Linux, and MacOS\. You must run CMake commands in a native Windows environment like cmd or PowerShell\. Running CMake commands in a virtual Linux environment, such as MSYS2 or WSL, isn't supported\.

Use CMake to generate the build files, and then use Make to build the application\.

**To generate the demo application's build files with CMake**

1. Navigate to the root of your FreeRTOS download directory\.

1. In a command line window, enter the following command to generate the build files\.

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_plus_ecc608a_devkitc -DCOMPILER=xtensa-esp32 -GNinja -S . -B your-build-directory
   ```
**Note**  
To build the application for debugging, add the `-DCMAKE_BUILD_TYPE=Debug` flag\.  
To generate the test application build files, add the `-DAFR_ENABLE_TESTS=1` flag\.

**To build the application**

1. Navigate to the `build` directory\.

1. In a command line window, enter the following command to invoke Ninja to build the application\.

   ```
   ninja
   ```

   Or, use the generic CMake interface to build the application\.

   ```
   cmake --build your-build-directory
   ```
**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

### Flash and run FreeRTOS<a name="flash-esp32wroom-32se"></a>

Use Espressif's IDF utility \(`freertos/vendors/espressif/esp-idf/tools/idf.py`\) to flash your board, run the application, and see logs\.

To erase the board's flash, navigate to the `freertos` directory and enter the following command\.

```
./vendors/espressif/esp-idf/tools/idf.py erase_flash -B build
```

To flash the application binary to your board, use `make`\.

```
make flash
```

You can also use the IDF script to flash your board\.

```
./vendors/espressif/esp-idf/tools/idf.py flash -B build
```

To monitor:

```
./vendors/espressif/esp-idf/tools/idf.py monitor -p /dev/ttyUSB1 -B build
```

**Tip**  
You can also combine these commands\.  

```
./vendors/espressif/esp-idf/tools/idf.py erase_flash flash monitor -p /dev/ttyUSB1 -B build
```

### Monitoring MQTT messages on the AWS Cloud<a name="gsg-esp32wroom-32se-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.
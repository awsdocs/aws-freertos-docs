# Getting started with the Espressif ESP32\-WROOM\-32SE<a name="getting_started_esp32wroom-32se"></a>

Follow this tutorial to get started with the Espressif ESP32\-WROOM\-32SE\. To purchase one from our partner on the AWS Partner Device catalog, see [ESP32\-WROOM\-32SE](https://devices.amazonaws.com/detail/a3G0h0000077nRtEAI/ESP32-WROOM-32SE)\.

**Note**  
Currently, the FreeRTOS port for ESP32\-WROOM\-32SE doesn't support the symmetric multiprocessing \(SMP\) feature\.

## Overview<a name="getting_started_esp32wroom-32se-overview"></a>

This tutorial guides you through the following steps:

1. Connect your board to a host machine

1. Install software on your host machine to develop and debug embedded applications for your microcontroller board

1. Cross\-compile a FreeRTOS demo application to a binary image

1. Load the application binary image to your board, and then run the application

1. Monitor and debug the running application using a serial connection

## Prerequisites<a name="setup-esp32wroom-32se-prereqs"></a>

Before you get started with FreeRTOS on your Espressif board, you must set up your AWS account and permissions\.

To create an account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an AWS Identity and Access Management \(IAM\) user to your account, see [Adding a user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) in the *IAM User Guide*\. To grant your IAM user permission to AWS IoT and FreeRTOS, attach the following IAM managed policies to your IAM users:
+ `AmazonFreeRTOSFullAccess` – Allows full access to all of your IAM user's FreeRTOS resources \(`freertos:*`\) 
+ `AWSIoTFullAccess` – Allows full access to all of your IAM user's AWS IoT resources \(`iot:*`\)\.

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

1. In the navigation pane, choose **Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM, see the [ IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide)\.

For more information about policies, see [Permissions and policies in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

**Note**  
The FreeRTOS 202012\.00 release supports ESP\-IDF v3\.3\. If you're using a later revision of FreeRTOS, follow the instructions specific to IDFv4\.2 at [Get started with the ESP\-IDF v4\.2](getting_started_espressif.md#setup-espressif-idf42)\.

## Get started with ESP\-IDF v3\.3<a name="setup-esp32wroom-32se-idf33"></a>

This section shows you how to use ESP\-IDF v3\.3 on Espressif hardware\. To use ESP\-IDF v4\.2, see [Get started with ESP\-IDF v4\.2](#setup-esp32wroom-32se-idf42)\.

### Set up the Espressif hardware<a name="setup-hw-espressif-esp32wroom-32se-idf33"></a>

See the [ ESP32\-DevKitC Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-devkitc.html) for information about setting up the ESP32\-WROOM\-32SE development board hardware\.

**Important**  
When you reach the **Get Started** section of the Espressif guide, stop and then follow the steps below\.

### Set up your development environment<a name="setup-env-esp32wroom-32se-idf33"></a>

To communicate with your board, you must download and install a toolchain\.

#### Setting up the toolchain<a name="setup-esp32wroom-32se-toolchain-idf33"></a>

**Note**  
ESP\-IDF v3\.3 \(the version that FreeRTOS uses\) doesn't support the latest version of the ESP32 compiler\. You must use the compiler that is compatible with version 3\.3 of the ESP\-IDF\. See the previous links\. To check the version of your compiler, run the following command\.  

```
xtensa-esp32-elf-gcc --version
```

To set up the toolchain, follow the instructions for your host machine's operating system:
+ [ Standard Setup of Toolchain \(ESP\-IDF v3\.3\) for Windows](https://docs.espressif.com/projects/esp-idf/en/v3.3/get-started-cmake/windows-setup.html)
+ [ Standard Setup of Toolchain \(ESP\-IDF v3\.3\) for macOS](https://docs.espressif.com/projects/esp-idf/en/v3.3/get-started-cmake/macos-setup.html)
+ [ Standard Setup of Toolchain \(ESP\-IDF v3\.3\) for Linux](https://docs.espressif.com/projects/esp-idf/en/v3.3/get-started-cmake/linux-setup.html)

**Important**  
When you reach the "Get ESP\-IDF" instructions under **Next Steps**, stop and return to the instructions on this page\.  
If you followed the "Get ESP\-IDF" instructions under **Next Steps**, just now or on a previous occasion, you should clear the `IDF_PATH` environment variable from your system before you continue\. This environment variable was automatically set if you followed the "Get ESP\-IDF" instructions\.

#### Install CMake<a name="gsg-esp32wroom-32se-cmake-idf33"></a>

The CMake build system is required to build the FreeRTOS demo and test applications for this device\. FreeRTOS supports versions 3\.13 and later\.

You can download the latest version of CMake from [ CMake\.org](https://cmake.org/download/)\. Source and binary distributions are available\.

For more details about using CMake with FreeRTOS, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

### Establish a serial connection<a name="esp32wroom-32se-establish-serial-connection-idf33"></a>

1. To establish a serial connection between your host machine and the ESP32\-WROOM\-32SE, install the CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [ Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\.

1. Follow the steps to [ Establish a Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html)\.

1. After you establish a serial connection, make a note of the serial port for your board's connection\. You need it when you build the demo\.

### Download and configure FreeRTOS<a name="download-and-configure-esp32wroom-32se-idf33"></a>

After you set up your environment, you can download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos) or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. For instructions, see the [README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file on the GitHub website\.

**Important**  
The ATECC608A device has a one\-time initialization that is locked onto the device the first time a project is run \(during the call to `C_InitToken`\)\. However, the FreeRTOS demo project and test project have different configurations\. If the device is locked during the demo project configurations, not all tests in the test project will succeed\.

1. Configure the FreeRTOS Demo Project by following the steps in [Configuring the FreeRTOS demos](freertos-prereqs.md#freertos-configure)\. Skip the last step **To format your AWS IoT credentials** and follow the steps below instead\.

1. Microchip has provided several scripting tools to help with the setup of the ATECC608A parts\. Navigate to the `freertos/vendors/microchip/secure_elements/app/example_trust_chain_tool` directory, and then open the `README.md` file\.

1. Follow the instructions in the `README.md` file to provision your device\. The steps include the following:

   1. Create and register a certificate authority with AWS

   1. Generate your keys on the ATECC608A and export the public key and device serial number

   1. Generate a certificate for the device and register that certificate with AWS

1. Load the CA certificate and device certificate onto the device by following the instructions for [Developer\-mode key provisioning](dev-mode-key-provisioning.md)\.

### Build, flash, and run the FreeRTOS demo project<a name="build-and-run-example-esp32wroom-32se-idf33"></a>

You can use CMake to generate the build files, Make to build the application binary, and Espressif's IDF utility to flash your board\.

#### Build FreeRTOS on Linux or MacOS \(ESP\-IDF v3\.3\)<a name="build-esp32wroom-32se-idf33"></a>

If you're using Windows, you can skip to [Build FreeRTOS on Windows \(ESP\-IDF v3\.3\)](#build-esp32wroom-32se-windows-idf33)\.

Use CMake to generate the build files, and then use Make to build the application\.

**To generate the demo application's build files with CMake**

1. Navigate to the root of your FreeRTOS download directory\.

1. In a command line window, enter the following command to generate the build files\.

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_plus_ecc608a_devkitc -DCOMPILER=xtensa-esp32 -S . -B your-build-directory
   ```
**Notes**  
To build the application for debugging, add the `-DCMAKE_BUILD_TYPE=Debug` flag\.  
To generate the test application build files, add the `-DAFR_ENABLE_TESTS=1` flag\.  
The code provided by Espressif uses the lightweight IP \(lwIP\) stack as the default networking stack\. To use the FreeRTOS\+TCP networking stack instead, add the `–DAFR_ESP_FREERTOS_TCP` flag to the CMake command\.  
To add the lwIP dependency for non\-vendor provided code, add the following lines to the CMake dependency file, `CMakeLists.txt`, for your custom Wi\-Fi component\.  

   ```
   # Add a dependency on the bluetooth espressif component to the common component
   set(COMPONENT_REQUIRES lwip)
   ```

**To build the application with Make**

1. Navigate to the `build` directory\.

1. In a command line window, enter the following command to build the application with Make\.

   ```
   make all -j4
   ```
**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

#### Build FreeRTOS on Windows \(ESP\-IDF v3\.3\)<a name="build-esp32wroom-32se-windows-idf33"></a>

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
The code provided by Espressif uses the lightweight IP \(lwIP\) stack as the default networking stack\. To use the FreeRTOS\+TCP networking stack instead, add the `–DAFR_ESP_FREERTOS_TCP` flag to the CMake command\.  
To add the lwIP dependency for non\-vendor provided code, add the following lines to the CMake dependency file, `CMakeLists.txt`, for your custom Wi\-Fi component\.  

   ```
   # Add a dependency on the bluetooth espressif component to the common component
   set(COMPONENT_REQUIRES lwip)
   ```

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

#### Flash and run FreeRTOS \(ESP\-IDF v3\.3\)<a name="flash-esp32wroom-32se-idf33"></a>

Use Espressif's IDF utility \(`freertos/vendors/espressif/esp-idf/tools/idf.py`\) to flash your board, run the application, and see logs\.

To erase the board's flash, navigate to the `freertos` directory and enter the following command\.

```
./vendors/espressif/esp-idf/tools/idf.py erase_flash -B build-directory
```

To flash the application binary to your board, use `make`\.

```
make flash
```

You can also use the IDF script to flash your board\.

```
./vendors/espressif/esp-idf/tools/idf.py flash -B build-directory
```

Use the following command to view logs:

```
./vendors/espressif/esp-idf/tools/idf.py monitor -p /dev/ttyUSB1 -B build-directory
```

**Tip**  
You can also combine these commands\.  

```
./vendors/espressif/esp-idf/tools/idf.py erase_flash flash monitor -p /dev/ttyUSB1 -B build-directory
```

## Get started with ESP\-IDF v4\.2<a name="setup-esp32wroom-32se-idf42"></a>

This section shows you how to use ESP\-IDF v4\.2 on Espressif hardware\. To use ESP\-IDF v3\.3, see [Get started with ESP\-IDF v3\.3](#setup-esp32wroom-32se-idf33)\.

### Set up the Espressif hardware<a name="setup-hw-espressif-esp32wroom-32se-idf42"></a>

For information about setting up the ESP32\-WROOM\-32SE development board hardware, see the [ ESP32\-DevKitC V4 Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/hw-reference/esp32/get-started-devkitc.html)\.

**Important**  
When you reach the **Get Started** section of the Espressif guides, stop, and then follow the steps below\.

### Set up your development environment<a name="setup-env-esp32wroom-32se-idf42"></a>

To communicate with your board, you need to download and install a toolchain\.

#### Set up the toolchain<a name="setup-esp32wroom-32se-toolchain-idf42"></a>

To set up the toolchain, follow the instructions for your host machine's operating system:
+ [ Standard Setup of Toolchain and prerequisites \(ESP\-IDF v4\.2\) for Windows](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/windows-setup.html)
+ [ Standard Setup of Toolchain prerequisites \(ESP\-IDF v4\.2\) for macOS](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/macos-setup.html)
+ [ Standard Setup of Toolchain prerequisites \(ESP\-IDF v4\.2\) for Linux](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/linux-setup.html)

**Important**  
When you reach the "Get ESP\-IDF" instructions under **Next Steps**, stop, and then return to the instructions on this page\.

##### Complete the installation \(Linux / Mac\)<a name="setup-esp32wroom-32se-toolchain-idf42-posix"></a>

The ESP\-IDF Windows installer installs all of the necessary tools\. The Linux and MAC OSX platforms require an additional step in order to complete the installation\.

Open a command line window, navigate to the FreeRTOS download directory, and then run the following script to download and install the espressif toolchain for your platform\.

```
vendors/espressif/esp-idf/install.sh
```

 Next, add the ESP\-IDF toolchain tools to your terminal's path with the following command\.

```
source vendors/espressif/esp-idf/export.sh
```

### Establish a serial connection<a name="esp32wroom-32se-establish-serial-connection-idf42"></a>

1. To establish a serial connection between your host machine and the ESP32\-WROOM\-32SE, install the CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [ Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\.

1. Follow the steps to [ Establish a Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/establish-serial-connection.html)\.

1. After you establish a serial connection, make a note of the serial port for your board's connection\. You need it when you build the demo\.

### Download and configure FreeRTOS<a name="download-and-configure-esp32wroom-32se-idf42"></a>

After you set up your environment, you can download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos) or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. For instructions, see the [README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file on the GitHub website\.

**Important**  
The ATECC608A device has a one\-time initialization that is locked onto the device the first time a project is run \(during the call to `C_InitToken`\)\. However, the FreeRTOS demo project and test project have different configurations\. If the device is locked during the demo project configurations, not all tests in the test project will succeed\.

1. Configure the FreeRTOS Demo Project by following the steps in [Configuring the FreeRTOS demos](freertos-prereqs.md#freertos-configure)\. When you get to the last step **To format your AWS IoT credentials** STOP and follow the steps below instead\.

1. Microchip has provided several scripting tools to help with the setup of the ATECC608A parts\. Navigate to the `freertos/vendors/microchip/secure_elements/app/example_trust_chain_tool` directory, and open the `README.md` file\.

1. Follow the instructions in the `README.md` file to provision your device\. The steps include the following:

   1. Create and register a certificate authority with AWS

   1. Generate your keys on the ATECC608A and export the public key and device serial number

   1. Generate a certificate for the device and register that certificate with AWS

1. Load the CA certificate and device certificate onto the device by following the instructions for [Developer\-mode key provisioning](dev-mode-key-provisioning.md)\.

### Build, flash, and run the FreeRTOS demo project using the idy\.py script<a name="build-and-run-example-esp32wroom-32se-idf42"></a>

You can use Espressif's IDF utility to generate the build files, build the application binary, and flash your board\.

#### Build and flash FreeRTOS on Windows, Linux, and MacOS \(ESP\-IDF v4\.2\)<a name="build-esp32wroom-32se-idf42"></a>

Use the `idf.py` script to build the project and flash the binaries onto your device\.

**Note**  
Some setups might require that you use the port option `"-p port-name"` with `idf.py` to specify the correct port, as in the following example\.  

```
idf.py -p /dev/cu.usbserial-00101301B flash
```

**To build and flash the project**

1. Navigate to the root of your FreeRTOS download directory\.

1. In a command line window, enter the following command to add the ESP\-IDF tools to your terminal's PATH:

   Windows

   ```
   vendors\espressif\esp-idf\export.bat
   ```

   Linux / MacOS

   ```
   source vendors/espressif/esp-idf/export.sh
   ```

1. Configure cmake in the `build` directory and build the firmware image with the following command\.

   ```
   idf.py -DVENDOR=espressif -DBOARD=esp32_ecc608a_devkitc -DCOMPILER=xtensa-esp32 build 
   ```

   You should see output like this following example\.

   ```
   Running cmake in directory /path/to/hello_world/build
   Executing "cmake -G Ninja --warn-uninitialized /path/to/hello_world"...
   Warn about uninitialized values.
   -- Found Git: /usr/bin/git (found version "2.17.0")
   -- Building empty aws_iot component due to configuration
   -- Component names: ...
   -- Component paths: ...
   
   ... (more lines of build system output)
   
   [527/527] Generating hello-world.bin
   esptool.py v2.3.1
   
   Project build complete. To flash, run this command:
   ../../../components/esptool_py/esptool/esptool.py -p (PORT) -b 921600 write_flash --flash_mode dio --flash_size detect --flash_freq 40m 0x10000 build/hello-world.bin  build 0x1000 build/bootloader/bootloader.bin 0x8000 build/partition_table/partition-table.bin
   or run 'idf.py -p PORT flash'
   ```

   If there are no errors, the build will generate the firmware binary \.bin files\.

1. Erase your development board's flash memory with the following command\.

   ```
   idf.py erase_flash 
   ```

1. Use the `idf.py` script to flash the application binary to your board\.

   ```
   idf.py flash
   ```

1. Monitor the output from your board's serial port with the following command\.

   ```
   idf.py monitor
   ```

**Note**  
You can combine these commands as in the following example\.  

```
idf.py erase_flash flash monitor 
```

**Note**  
For certain host machine setups, you must specify the port when you flash the board as in the following example\.  

```
idf.py erase_flash flash monitor -p /dev/ttyUSB1
```

### Build and Flash FreeRTOS with CMake<a name="flash-esp32wroom-32se-cmake-idf42"></a>

Besides using the idf\.py script provided by the IDF SDK to build and run your code, you can also build the project with CMake\. Currently it supports Unix Makefile and the Ninja build system\.

**To build and flash the project**

1. In a command line window, navigate to the root of your FreeRTOS download directory\.

1. Run the following script to add the ESP\-IDF tools to your shell's PATH\.

   Windows

   ```
   vendors\espressif\esp-idf\export.bat
   ```

   Linux / MacOS

   ```
   source vendors/espressif/esp-idf/export.sh
   ```

1. Enter the following command to generate the build files\.

   With Unix Makefiles 

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_plus_ecc608a_devkitc -DCOMPILER=xtensa-esp32 -S . -B ./YOUR_BUILD_DIRECTORY -DAFR_ENABLE_ALL_MODULES=1 -DAFR_ENABLE_TESTS=0
   ```

   With Ninja

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_plus_ecc608a_devkitc -DCOMPILER=xtensa-esp32 -S . -B ./YOUR_BUILD_DIRECTORY -DAFR_ENABLE_ALL_MODULES=1 -DAFR_ENABLE_TESTS=0 -GNinja
   ```

1. Erase the flash and then flash the board\.

   With Unix Makefiles 

   ```
   make -C ./YOUR_BUILD_DIRECTORY erase_flash
   ```

   ```
   make -C ./YOUR_BUILD_DIRECTORY flash
   ```

   With Ninja

   ```
   ninja -C ./YOUR_BUILD_DIRECTORY erase_flash
   ```

   ```
   ninja -C ./YOUR_BUILD_DIRECTORY flash
   ```

## Monitoring MQTT messages on the AWS Cloud<a name="gsg-esp32wroom-32se-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter ***your\-thing\-name*/example/topic**, and then choose **Subscribe to topic**\.

## Additional information<a name="getting_started_esp32wroom-32se-additional"></a>

For additional information on using and troubleshooting Espressif ESP32 boards, see the following topics:
+  [Using FreeRTOS in your own CMake project for ESP32](getting_started_espressif.md#getting_started_espressif_cmake_project) 
+  [Troubleshooting](getting_started_espressif.md#getting_started_espressif_troubleshooting) 
+  [Debugging](getting_started_espressif.md#getting_started_espressif_debugging) 
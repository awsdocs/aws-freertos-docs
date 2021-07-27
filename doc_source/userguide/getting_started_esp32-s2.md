# Getting started with the Espressif ESP32\-S2<a name="getting_started_esp32-s2"></a>

This tutorial shows you how to get started with the Espressif ESP32\-S2 SoC and [ESP32\-S2\-Saola\-1](https://devices.amazonaws.com/detail/a3G0h00000AkFngEAF/ESP32-S2-Saola-1) development boards\.

## Overview<a name="getting_started_esp32-s2-overview"></a>

This tutorial guides you through the following steps:

1. Connect your board to a host machine\.

1. Install software on your host machine to develop and debug embedded applications for your microcontroller board\.

1. Cross\-compile a FreeRTOS demo application to a binary image\.

1. Load the application binary image to your board, and then run the application\.

1. Monitor and debug the running application using a serial connection\.

## Prerequisites<a name="setup-esp32-s2-prereqs"></a>

Before you get started with FreeRTOS on your Espressif board, you must set up your AWS account and permissions\.

To create an account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an AWS Identity and Access Management \(IAM\) user to your account, see [Adding a user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) in the *IAM User Guide*\. To grant your IAM user permission to AWS IoT and FreeRTOS, attach the following IAM managed policies to your IAM users:
+ `AmazonFreeRTOSFullAccess` – Allows full access to all of your IAM user's FreeRTOS resources \(`freertos:*`\)\.
+ `AWSIoTFullAccess` – Allows full access to all of your IAM user's AWS IoT resources \(`iot:*`\)\.

**To attach the `AmazonFreeRTOSFullAccess` policy to your IAM user**

1. Navigate to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose **Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To attach the `AWSIoTFullAccess` policy to your IAM user**

1. Navigate to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose **Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter `AWSIoTFullAccess`, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide)\.

For more information about policies, see [Permissions and policies in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

## Get started with ESP\-IDF v4\.2<a name="setup-esp32-s2-idf42"></a>

**Note**  
The Linux commands in this tutorial require that you use the Bash shell\.

1. Set up the Espressif hardware\.

   For information about setting up the ESP32\-S2 development board hardware, see the [ ESP32\-S2\-Saola\-1 Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32s2/hw-reference/esp32s2/user-guide-saola-1-v1.2.html)\.
**Important**  
When you reach the **Get Started** section of the Espressif guides, stop, and then perform the following steps\.

1. Set up your development environment\.

   To communicate with your board, you must download and install a toolchain\. Follow the instructions for your host machine's operating system:
   + [ Standard Setup of Toolchain and prerequisites \(ESP\-IDF v4\.2\) for Windows](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32s2/get-started/windows-setup.html)\.
   + [ Standard Setup of Toolchain prerequisites \(ESP\-IDF v4\.2\) for macOS](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32s2/get-started/macos-setup.html)\.
   + [ Standard Setup of Toolchain prerequisites \(ESP\-IDF v4\.2\) for Linux](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32s2/get-started/linux-setup.html)\.
**Important**  
When you reach the "Get ESP\-IDF" instructions under **Next Steps**, stop, and then return to the instructions on this page\.

1. Complete the installation \(Linux / macOS\)\.

   The ESP\-IDF Windows installer installs all of the necessary tools\. The Linux and mac OS X platforms require an additional step in order to complete the installation\. Please follow the steps below after you [Download and configure FreeRTOS](#download-and-configure-esp32-s2-idf42)\.

   1. Open a command line window, navigate to the FreeRTOS download directory, and then run the following script to download and install the espressif toolchain for your platform\.

      ```
      vendors/espressif/esp-idf/install.sh
      ```

   1. Next, add the ESP\-IDF toolchain tools to your terminal's path with the following command\.

      ```
      source vendors/espressif/esp-idf/export.sh
      ```

1. Establish a serial connection\.

   1. To establish a serial connection between your host machine and the ESP32\-S2, install the CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [ Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\.

   1. Follow the steps to [ Establish a Serial Connection with ESP32\-S2](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/establish-serial-connection.html)\.

   1. After you establish a serial connection, make a note of the serial port for your board's connection\. You will need it when you build the demo\.

### Download and configure FreeRTOS<a name="download-and-configure-esp32-s2-idf42"></a>

After you set up your environment, you can download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos) or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. For instructions, see the [README\.md](https://github.com/aws/amazon-freertos/blob/main/README.md) file on the GitHub website\.

**Configure the FreeRTOS demo applications**

1. If you're running macOS or Linux, open a terminal prompt\. If you're running Windows, open `mingw32.exe`\. \([ MinGW](https://sourceforge.net/projects/mingw-w64/files/) is a minimalist development environment for native Microsoft Windows applications\.\)

1. To verify that you have Python3 installed, run the following:

   ```
   python --version
   ```

   The version installed is displayed\. If you don't have Python 3\.0\.1 or later installed, you can install it from the [Python](https://www.python.org/downloads/) website\.

1. You need the AWS Command Line Interface \(CLI\) to run AWS IoT commands\. If you're running Windows, use the easy\_install awscli to install the AWS CLI in the mingw32 environment\.

   If you're running macOS or Linux, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

1. Run

   ```
   aws configure
   ```

   and configure the AWS CLI with your AWS access key ID, secret access key, and default AWS Region\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

1. Use the following command to install the AWS SDK for Python \(boto3\):
   + On Windows, in the mingw32 environment, run

     ```
     easy_install boto3
     ```
   + On macOS or Linux, run

     ```
     pip install tornado nose --user
     ```

     and then run

     ```
     pip install boto3 --user
     ```

FreeRTOS includes the `SetupAWS.py` script to make it easier to set up your Espressif board to connect to AWS IoT\.

**To run the configuration script**

1. To configure the script, open `freertos/tools/aws_config_quick_start/configure.json` and set the following attributes:   
**`afr_source_dir`**  
The complete path to the `freertos` directory on your computer\. Make sure that you use forward slashes to specify this path\.  
**`thing_name`**  
The name that you want to assign to the AWS IoT thing that represents your board\.  
**`wifi_ssid`**  
The SSID of your Wi\-Fi network\.  
**`wifi_password`**  
The password for your Wi\-Fi network\.  
**`wifi_security`**  
The security type for your Wi\-Fi network\. The following are valid security types:  
   + `eWiFiSecurityOpen` \(Open, no security\)
   + `eWiFiSecurityWEP` \(WEP security\)
   + `eWiFiSecurityWPA` \(WPA security\)
   + `eWiFiSecurityWPA2` \(WPA2 security\)

1. If you're running macOS or Linux, open a terminal prompt\. If you're running Windows, open the `mingw32.exe` file\.

1. Navigate to the `freertos/tools/aws_config_quick_start` directory and run

   ```
   python SetupAWS.py setup
   ```

   The script does the following:
   + Creates an AWS IoT thing, certificate, and policy\.
   + Attaches the AWS IoT policy to the certificate and the certificate to the AWS IoT thing\.
   + Populates the `aws_clientcredential.h` file with your AWS IoT endpoint, Wi\-Fi SSID, and credentials\.
   + Formats your certificate and private key and writes them to the `aws_clientcredential_keys.h` header file\.
**Note**  
The certificate is hardcoded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

   For more information about `SetupAWS.py`, see the `README.md` in the `freertos/tools/aws_config_quick_start` directory\.

### Build, flash, and run the FreeRTOS demo project using the idf\.py script<a name="build-and-run-example-esp32-s2-idf42"></a>

You can use Espressif's IDF utility to generate the build files, build the application binary, and flash your board\.

#### Build and flash FreeRTOS on Windows, Linux, and macOS \(ESP\-IDF v4\.2\)<a name="build-esp32-s2-idf42"></a>

Use the `idf.py` script to build the project and flash the binaries onto your device\.

**Note**  
Some setups might require that you use the port option `-p port-name` with `idf.py` to specify the correct port, as in the following example\.  

```
idf.py -p /dev/cu.usbserial-00101301B flash
```

**To build and flash the project**

1. Navigate to the root of your FreeRTOS download directory\.

1. In a command line window, enter the following command to add the ESP\-IDF tools to your terminal's PATH: 
   + Windows

     ```
     vendors\espressif\esp-idf\export.bat
     ```
   + Linux / MacOS

     ```
     source vendors/espressif/esp-idf/export.sh
     ```

1. Configure cmake in the `build` directory and build the firmware image with the following command\.

   ```
   idf.py -DVENDOR=espressif -DBOARD=esp32s2_saola_1 -DCOMPILER=xtensa-esp32s2 build
   ```

   You should see output like this following example\.

   ```
   Executing action: all (aliases: build)
      Running cmake in directory /path/to/hello_world/build
      Executing "cmake -G Ninja -DPYTHON_DEPS_CHECKED=1 -DESP_PLATFORM=1 -DVENDOR=espressif -DBOARD=esp32s2_saola_1 -DCOMPILER=xtensa-esp32s2 -DCCACHE_ENABLE=0 /path/to/hello_world"...
      -- The C compiler identification is GNU 8.4.0
      -- The CXX compiler identification is GNU 8.4.0
      -- The ASM compiler identification is GNU
   
      ... (more lines of build system output)
   
      [1628/1628] Generating binary image from built executable
      esptool.py v3.0
      Generated /path/to/hello_world/build/aws_demos.bin
   
      Project build complete. To flash, run this command:
      esptool.py -p (PORT) -b 460800 --before default_reset --after hard_reset --chip esp32s2  write_flash --flash_mode dio --flash_size detect --flash_freq 80m 0x1000 build/bootloader/bootloader.bin 0x8000 build/partition_table/partition-table.bin 0x16000 build/ota_data_initial.bin 0x20000 build/aws_demos.bin
      or run 'idf.py -p (PORT) flash'
   ```

   If there are no errors, the build generates the firmware binary \.bin files\.

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
For certain host machine setups, you must specify the port when you flash the board as in the following example\.  

     ```
     idf.py erase_flash flash monitor -p /dev/ttyUSB1
     ```

### Build and Flash FreeRTOS with CMake<a name="flash-esp32-s2-cmake-idf42"></a>

Besides using the `idf.py` script provided by the IDF SDK to build and run your code, you can also build the project with CMake\. Currently it supports Unix Makefile and the Ninja build system\.

**To build and flash the project**

1. In a command line window, navigate to the root of your FreeRTOS download directory\.

1. Run the following script to add the ESP\-IDF tools to your shell's PATH\.
   + Windows

     ```
     vendors\espressif\esp-idf\export.bat
     ```
   + Linux / macOS 

     ```
     source vendors/espressif/esp-idf/export.sh
     ```

1. Enter the following command to generate the build files\.
   + With Unix Makefiles

     ```
     cmake -DVENDOR=espressif -DBOARD=esp32s2_saola_1 -DCOMPILER=xtensa-esp32s2 -S . -B ./YOUR_BUILD_DIRECTORY -DAFR_ENABLE_ALL_MODULES=1 -DAFR_ENABLE_TESTS=0
     ```
   + With Ninja

     ```
     cmake -DVENDOR=espressif -DBOARD=esp32s2_saola_1 -DCOMPILER=xtensa-esp32s2 -S . -B ./YOUR_BUILD_DIRECTORY -DAFR_ENABLE_ALL_MODULES=1 -DAFR_ENABLE_TESTS=0 -GNinja
     ```

1. Build the project\.
   + With Unix Makefiles

     ```
     make -C ./YOUR_BUILD_DIRECTORY -j8
     ```
   + With Ninja

     ```
     ninja -C ./YOUR_BUILD_DIRECTORY -j8
     ```

1. Erase the flash and then flash the board\.
   + With Unix Makefiles

     ```
     make -C ./YOUR_BUILD_DIRECTORY erase_flash
     ```

     ```
     make -C ./YOUR_BUILD_DIRECTORY flash
     ```
   + With Ninja

     ```
     ninja -C ./YOUR_BUILD_DIRECTORY erase_flash
     ```

     ```
     ninja -C ./YOUR_BUILD_DIRECTORY flash
     ```

## Monitoring MQTT messages on the AWS Cloud<a name="gsg-esp32-s2-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter `your-thing-name/example/topic`, and then choose **Subscribe to topic**\.

## Additional information<a name="getting_started_esp32-s2-additional"></a>

For more information about using and troubleshooting Espressif ESP32 boards, see the following topics:
+ [Using FreeRTOS in your own CMake project for ESP32](getting_started_espressif.md#getting_started_espressif_cmake_project)
+ [Troubleshooting](getting_started_espressif.md#getting_started_espressif_troubleshooting)
+ [Debugging](getting_started_espressif.md#getting_started_espressif_debugging)
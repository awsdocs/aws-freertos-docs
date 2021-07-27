# Getting started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT<a name="getting_started_espressif"></a>

Follow this tutorial to get started with the Espressif ESP32\-DevKitC equipped with ESP32\-WROOM\-32, ESP32\-SOLO\-1, or ESP\-WROVER modules and the ESP\-WROVER\-KIT\-VB\. To purchase one from our partner on the AWS Partner Device catalog, use the following links:
+ [ESP32\-WROOM\-32 DevKitC](https://devices.amazonaws.com/detail/a3G0L00000AANtjUAH/ESP32-DevKitC)
+ [ESP32\-SOLO\-1](https://devices.amazonaws.com/detail/a3G0h0000076lSMEAY)
+ [ ESP32\-WROVER\-KIT](https://devices.amazonaws.com/detail/a3G0L00000AANtlUAH/ESP-WROVER-KIT)

These versions of development boards are supported on FreeRTOS\.

For more information about the latest versions of these boards, see [ ESP32\-DevKitC V4](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/hw-reference/modules-and-boards.html#esp32-devkitc-v4) or [ ESP\-WROVER\-KIT v4\.1](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/hw-reference/modules-and-boards.html#esp-wrover-kit-v4-1) on the Espressif website\.

**Note**  
Currently, the FreeRTOS port for ESP32\-WROVER\-KIT and ESP DevKitC doesn't support the Symmetric multiprocessing \(SMP\) feature\.

## Overview<a name="getting_started_espressif-overview"></a>

This tutorial guides you through the following steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Prerequisites<a name="setup-espressif-prereqs"></a>

Before you get started with FreeRTOS on your Espressif board, you must set up your AWS account and permissions\. To create an account, see [ Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an AWS Identity and Access Management \(IAM\) user to your account, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. To grant your IAM user account access to AWS IoT and FreeRTOS, attach the following IAM policies to your IAM user account:
+ `AmazonFreeRTOSFullAccess`

  Allows full access to all of your IAM user's FreeRTOS resources \(freertos:\*\)\.
+ `AWSIoTFullAccess`

  Allows full access to all of your IAM user's AWS IoT resources \(iot:\*\)\.

**To attach the AmazonFreeRTOSFullAccess policy to your IAM user**

1. Navigate to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose **Users**\.

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

For more information about IAM and user accounts, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

For more information about policies, see [Permissions and policies in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

## Get started with the ESP\-IDF v4\.2<a name="setup-espressif-idf42"></a>

**Note**  
The Linux commands in this tutorial require that you use the Bash shell\.

1. Set up the Espressif hardware\.

   For information about setting up the ESP32\-DevKitC development board hardware, see the [ ESP32\-DevKitC V4 Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/hw-reference/esp32/get-started-devkitc.html)\.

   For information about setting up the ESP\-WROVER\-KIT development board hardware, see the [ ESP\-WROVER\-KIT V4\.1 Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/hw-reference/esp32/get-started-wrover-kit.html)\.
**Important**  
When you reach the **Get Started** section of the Espressif guides, stop, and then return to the instructions on this page\.

1. Set up your development environment\.

   To communicate with your board, you must download and install a toolchain\. Follow these instructions for your host machine's operating system:
   + [ Standard Setup of Toolchain and prerequisites \(ESP\-IDF v4\.2\) for Windows](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/windows-setup.html)\.
   + [ Standard Setup of Toolchain prerequisites \(ESP\-IDF v4\.2\) for macOS](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/macos-setup.html)\.
   + [ Standard Setup of Toolchain prerequisites \(ESP\-IDF v4\.2\) for Linux](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/linux-setup.html)\.
**Important**  
When you reach the "Get ESP\-IDF" instructions under **Next Steps**, stop, and then return to the instructions on this page\.

1. Complete the installation \(Linux / macOS\)\.

   The ESP\-IDF Windows installer installs all of the necessary tools\. The Linux and macOS platforms require an additional step to complete the installation\. Please follow the steps below after you [Download and configure FreeRTOS](#download-and-configure-espressif-idf42)\. 

   1. Open a command line window\.

   1. Navigate to the FreeRTOS download directory, and then run the following script to download and install the espressif toolchain for your platform\.

      ```
      vendors/espressif/esp-idf/install.sh
      ```

   1. Add the ESP\\\-IDF toolchain tools to your terminal's path with the following command\.

      ```
      source vendors/espressif/esp-idf/export.sh
      ```

1. Establish a serial connection\.

   To establish a serial connection between your host machine and the ESP32\-DevKitC, you must install the CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [ Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\.

   To establish a serial connection between your host machine and the ESP32\-WROVER\-KIT, you must install the FTDI virtual COM port driver\. You can download this driver from [FTDI](https://www.ftdichip.com/Drivers/VCP.htm)\.

   For more information, see [ Establish Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/get-started/establish-serial-connection.html)\. After you establish a serial connection, make a note of the serial port for your board's connection\. You need it when you build the demo\.

### Download and configure FreeRTOS<a name="download-and-configure-espressif-idf42"></a>

After you set up your environment, you can download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos), or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. For instructions, see the [README\.md](https://github.com/aws/amazon-freertos/blob/main/README.md) file on the GitHub website\.

**Configure the FreeRTOS demo applications**

1. If you're running macOS or Linux, open a terminal prompt\. If you're running Windows, open `mingw32.exe`\. \([ MinGW](https://sourceforge.net/projects/mingw-w64/files/) is a minimalist development environment for native Microsoft Windows applications\.\) 

1. To verify that you have Python3 installed, run 

   ```
   python --version
   ```

   The version installed is displayed\. If you don't have Python 3\.0\.1 or later installed, you can install it from the [Python](https://www.python.org/downloads/) website\.

1. You need the AWS Command Line Interface \(CLI\) to run AWS IoT commands\. If you're running Windows, use the `easy_install awscli` to install the AWS CLI in the mingw32 environment\.

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

   FreeRTOS includes the `SetupAWS.py` script to make it easier to set up your Espressif board to connect to AWS IoT\. To configure the script, open `freertos/tools/aws_config_quick_start/configure.json` and set the following attributes:  
**`afr_source_dir`**  
The complete path to the `freertos` directory on your computer\. Make sure that you use forward slashes to specify this path\.  
**`thing_name`**  
The name that you want to assign to the AWS IoT thing that represents your board\.  
**`wifi_ssid`**  
The SSID of your Wi\-Fi network\.  
**`wifi_password`**  
The password for your Wi\-Fi network\.  
**`wifi_security`**  
The security type for your Wi\-Fi network\.  
The following are valid security types:  
   + `eWiFiSecurityOpen` \(Open, no security\)
   + `eWiFiSecurityWEP` \(WEP security\)
   + `eWiFiSecurityWPA` \(WPA security\)
   + `eWiFiSecurityWPA2` \(WPA2 security\)

1. Run the configuration script\.

   1. If you're running macOS or Linux, open a terminal prompt\. If you're running Windows, open the `mingw32.exe` file\.

   1. Navigate to the `freertos/tools/aws_config_quick_start` directory and run 

      ```
      python SetupAWS.py setup
      ```

      The script does the following:
      + Creates an IoT thing, certificate, and policy\.
      + Attaches the IoT policy to the certificate and the certificate to the AWS IoT thing\.
      + Populates the `aws_clientcredential.h` file with your AWS IoT endpoint, Wi\-Fi SSID, and credentials\.
      + Formats your certificate and private key and writes them to the `aws_clientcredential_keys.h` header file\.
**Note**  
The certificate is hardcoded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

      For more information about `SetupAWS.py`, see the `README.md` in the `freertos/tools/aws_config_quick_start` directory\.

### Build, flash, and run the FreeRTOS demo project using the idf\.py script<a name="build-and-run-example-espressif-idf42"></a>

You can use Espressif's IDF utility \(`idf.py`\) to build the project and flash the binaries onto your device\.

**Note**  
Some setups might require that you use the port option `"-p port-name"` with `idf.py` to specify the correct port, as in the following example\.  

```
idf.py -p /dev/cu.usbserial-00101301B flash
```

**Build and flash FreeRTOS on Windows, Linux, and macOS \(ESP\-IDF v4\.2\)**

1. Navigate to the root of your FreeRTOS download directory\.

1. In a command line window, enter the following command to add the ESP\-IDF tools to your terminal's PATH\.  
**Windows**  

   ```
   vendors\espressif\esp-idf\export.bat
   ```  
**Linux / macOS**  

   ```
   source vendors/espressif/esp-idf/export.sh
   ```

1. Configure cmake in the `build` directory and build the firmware image with the following command\.

   ```
   idf.py -DVENDOR=espressif -DBOARD=esp32_wrover_kit -DCOMPILER=xtensa-esp32 build
   ```

   You should see output like the following\.

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
You can combine these commands such as in the following example\.  

   ```
   idf.py erase_flash flash monitor
   ```

   For certain host machine setups, you must specify the port when you flash the board such as in the following example\.

   ```
   idf.py erase_flash flash monitor -p /dev/ttyUSB1
   ```

### Build and Flash FreeRTOS with CMake<a name="flash-espressif-cmake-idf42"></a>

In addition to the `idf.py` script provided by the IDF SDK to build and run your code, you can also build the project with CMake\. Currently, it supports either Unix Makefiles or the Ninja build system\.

**To build and flash the project**

1. In a command line window, navigate to the root of your FreeRTOS download directory\.

1. Run the following script to add the ESP\-IDF tools to your shell's PATH\.  
**Windows**  

   ```
   vendors\espressif\esp-idf\export.bat
   ```  
**Linux / macOS**  

   ```
   source vendors/espressif/esp-idf/export.sh
   ```

1. Enter the following command to generate the build files\.  
**With Unix Makefiles**  

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_wrover_kit -DCOMPILER=xtensa-esp32 -S . -B ./YOUR_BUILD_DIRECTORY -DAFR_ENABLE_ALL_MODULES=1 -DAFR_ENABLE_TESTS=0
   ```  
**With Ninja**  

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_wrover_kit -DCOMPILER=xtensa-esp32 -S . -B ./YOUR_BUILD_DIRECTORY -DAFR_ENABLE_ALL_MODULES=1 -DAFR_ENABLE_TESTS=0 -GNinja
   ```

1. Build the project\.  
**With Unix Makefiles**  

   ```
   make -C ./YOUR_BUILD_DIRECTORY -j8 
   ```  
**With Ninja**  

   ```
   ninja -C ./YOUR_BUILD_DIRECTORY -j8
   ```

1. Erase the flash and then flash the board\.  
**With Unix Makefiles**  

   ```
   make -C ./YOUR_BUILD_DIRECTORY erase_flash
   ```

   ```
   make -C ./YOUR_BUILD_DIRECTORY flash
   ```  
**With Ninja**  

   ```
   ninja -C ./YOUR_BUILD_DIRECTORY erase_flash
   ```

   ```
   ninja -C ./YOUR_BUILD_DIRECTORY flash
   ```

## Monitoring MQTT messages on the cloud<a name="gsg-espressif-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Navigate to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. To open the MQTT client, in the navigation pane, choose **Test**\.

1. In **Subscription topic**, enter `your-thing-name/example/topic`, and then choose **Subscribe to topic**\.

## Run the Bluetooth Low Energy demos<a name="espressif-run-ble"></a>

FreeRTOS supports [Bluetooth Low Energy library](freertos-ble-library.md) connectivity\.

To run the FreeRTOS demo project across Bluetooth Low Energy, you must run the FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application on an iOS or Android mobile device\.

**To set up the FreeRTOS Bluetooth Low Energy mobile SDK demo application**

1. Follow the instructions in [Mobile SDKs for FreeRTOS Bluetooth devices](freertos-ble-mobile.md) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](ble-demo.md#ble-sdk-app) to set up the demo mobile application on your mobile device\.

For instructions about how to run the MQTT over Bluetooth Low Energy demo on your board, see [MQTT over Bluetooth Low Energy](ble-demo.md#ble-demo-mqtt)\.

For instructions about how to run the Wi\-Fi provisioning demo on your board, see [Wi\-Fi provisioning](ble-demo.md#ble-demo-wifi)\.

## Using FreeRTOS in your own CMake project for ESP32<a name="getting_started_espressif_cmake_project"></a>

If you want to consume FreeRTOS in your own CMake project, you can set it up as a subdirectory and build it together with your application\. First, get a copy of FreeRTOS either from [GitHub](https://github.com/aws/amazon-freertos) or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. If you're using Git, you can also set it up as a Git submodule with the following command so it's easier to update in the future\.

```
git submodule add -b release https://github.com/aws/amazon-freertos.git freertos
```

If a later version is released, you can update your local copy with these commands\.

```
# Pull the latest changes from the remote tracking branch.
git submodule update --remote -- freertos
```

```
# Commit the submodule change because it is pointing to a different revision now.
git add freertos
```

```
git commit -m "Update FreeRTOS to a new release"
```

If your project has the following directory structure: 

```
- freertos (the copy that you obtained from GitHub or the AWS IoT console)
- src
  - main.c (your application code)
- CMakeLists.txt
```

Then the following is an example of the top\-level `CMakeLists.txt` file that can be used to build your application together with FreeRTOS\.

```
cmake_minimum_required(VERSION 3.13)

project(freertos_examples)

add_executable(my_app src/main.c)

# Tell IDF build to link against this target.
set(IDF_PROJECT_EXECUTABLE my_app)

# Add FreeRTOS as a subdirectory. AFR_BOARD tells which board to target.
set(AFR_BOARD espressif.esp32_devkitc CACHE INTERNAL "")
add_subdirectory(freertos)

# Link against the mqtt library so that we can use it. Dependencies are transitively
# linked.
target_link_libraries(my_app PRIVATE AFR::mqtt)
```

To build the project, run the following CMake commands\. Make sure the ESP32 compiler is in the PATH environment variable\.

```
cmake -S . -B build-directory -DCMAKE_TOOLCHAIN_FILE=freertos/tools/cmake/toolchains/xtensa-esp32.cmake -GNinja 
```

```
cmake --build build 
```

To flash the application to your board, run the following command\.

```
cmake --build build-directory --target flash 
```

### Using components from FreeRTOS<a name="getting_started_espressif_cmake_project_components"></a>

After running CMake, you can find all available components in the summary output\. It should look something like the following example\.

```
====================Configuration for FreeRTOS====================
  Version:                 202107.00
  Git version:             202107.00-g79ad6defb

Target microcontroller:
  vendor:                  Espressif
  board:                   ESP32-DevKitC
  description:             Development board produced by Espressif that comes in two
                           variants either with ESP-WROOM-32 or ESP32-WROVER module
  family:                  ESP32
  data ram size:           520KB
  program memory size:     4MB

Host platform:
  OS:                      Linux-4.15.0-66-generic
  Toolchain:               xtensa-esp32
  Toolchain path:          /opt/xtensa-esp32-elf
  CMake generator:         Ninja

FreeRTOS modules:
  Modules to build:        backoff_algorithm, common, common_io, core_http,
                           core_http_demo_dependencies, core_json, core_mqtt,
                           core_mqtt_agent, core_mqtt_agent_demo_dependencies,
                           core_mqtt_demo_dependencies, crypto, defender, dev_mode_key_
                           provisioning, device_defender, device_defender_demo_
                           dependencies, device_shadow, device_shadow_demo_dependencies,
                           freertos_cli_plus_uart, freertos_plus_cli, greengrass,
                           http_demo_helpers, https, jobs, jobs_demo_dependencies,
                           kernel, logging, mqtt, mqtt_agent_interface, mqtt_demo_
                           helpers, mqtt_subscription_manager, ota, ota_demo_
                           dependencies, ota_demo_version, pkcs11, pkcs11_helpers,
                           pkcs11_implementation, pkcs11_utils, platform, secure_sockets,
                           serializer, shadow, tls, transport_interface_secure_sockets,
                           wifi
  Enabled by user:         common_io, core_http_demo_dependencies, core_json,
                           core_mqtt_agent_demo_dependencies, core_mqtt_demo_
                           dependencies, defender, device_defender, device_defender_demo_
                           dependencies, device_shadow, device_shadow_demo_dependencies,
                           freertos_cli_plus_uart, freertos_plus_cli, greengrass, https,
                           jobs, jobs_demo_dependencies, logging, ota_demo_dependencies,
                           pkcs11, pkcs11_helpers, pkcs11_implementation, pkcs11_utils,
                           platform, secure_sockets, shadow, wifi
  Enabled by dependency:   backoff_algorithm, common, core_http, core_mqtt,
                           core_mqtt_agent, crypto, demo_base, dev_mode_key_provisioning,
                           freertos, http_demo_helpers, kernel, mqtt, mqtt_agent_
                           interface, mqtt_demo_helpers, mqtt_subscription_manager, ota,
                           ota_demo_version, pkcs11_mbedtls, serializer, tls,
                           transport_interface_secure_sockets, utils
  3rdparty dependencies:   jsmn, mbedtls, pkcs11, tinycbor
  Available demos:         demo_cli_uart, demo_core_http, demo_core_mqtt, demo_core_mqtt_
                           agent, demo_device_defender, demo_device_shadow,
                           demo_greengrass_connectivity, demo_jobs, demo_ota_core_http,
                           demo_ota_core_mqtt, demo_tcp
  Available tests:
=========================================================================
```

You can reference any components from the `Modules to build` list\. To link them into your application, put the `AFR::` namespace in front of the name, for example, `AFR::mqtt`, `AFR::ota`, and so on\.

### Add custom components to ESP\-IDF<a name="getting_started_espressif_cmake_project_espidf"></a>

You can add more components to the ESP\-IDF build environment\. For example, assuming you want to add a component called `example_component`, and your project looks like this:

```
- freertos
- components
  - example_component
    - include
      - example_component.h
    - src
      - example_component.c
    - CMakeLists.txt
- src
  - main.c
- CMakeLists.txt
```

The following is an example of the `CMakeLists.txt` file for your component\.

```
# include paths of this components.
set(COMPONENT_ADD_INCLUDEDIRS include)

# source files of this components.
set(COMPONENT_SRCDIRS src)
# Alternatively, use COMPONENT_SRCS to specify source files explicitly
# set(COMPONENT_SRCS src/example_component.c)

# add this components, this will define a CMake library target.
register_component()
```

You can also specify dependencies by using the standard CMake function `target_link_libraries`\. Note that the target name for your component is stored in the variable `COMPONENT_TARGET`, defined by the ESP\-IDF\.

```
# add this component, this will define a CMake library target.
register_component()

# standard CMake function can be used to specify dependencies. ${COMPONENT_TARGET} is defined
# from esp-idf when you call register_component, by default it's idf_component_<folder_name>.
target_link_libraries(${COMPONENT_TARGET} PRIVATE AFR::mqtt)
```

For ESP components, this is done by setting two variables, `COMPONENT_REQUIRES` and `COMPONENT_PRIV_REQUIRES`\. For information, see [Build System \(CMake\)](https://docs.espressif.com/projects/esp-idf/en/v4.2/api-guides/build-system-cmake.html)\) in the *ESP\-IDF Programming Guide v4\.2*\.

```
# If the dependencies are from ESP-IDF, use these 2 variables. Note these need to be
# set before calling register_component().
set(COMPONENT_REQUIRES log)
set(COMPONENT_PRIV_REQUIRES lwip)
```

Then, in the top level `CMakeLists.txt` file, you tell ESP\-IDF where to find these components\. Add the following lines anywhere before `add_subdirectory(freertos)`\.

```
# Add some extra components. IDF_EXTRA_COMPONENT_DIRS is a variable used by ESP-IDF
# to collect extra components.
get_filename_component(
    EXTRA_COMPONENT_DIRS
    "components/example_component" ABSOLUTE
)
list(APPEND IDF_EXTRA_COMPONENT_DIRS ${EXTRA_COMPONENT_DIRS})
```

This component is now automatically linked to your application code by default\. You can now include its header files and call the functions it defines\.

### Override the configurations for FreeRTOS<a name="getting_started_espressif_cmake_project_override"></a>

There's currently no well\-defined approach to redefining the configs outside of the FreeRTOS source tree\. By default, CMake will look for the `freertos/vendors/espressif/boards/esp32/aws_demos/config_files/` and `freertos/demos/include/` directories\. However, you can use a workaround to tell the compiler to search other directories first\. For example, you can add another folder for FreeRTOS configurations\.

```
- freertos
- freertos-configs
  - aws_clientcredential.h
  - aws_clientcredential_keys.h
  - iot_mqtt_agent_config.h
  - iot_config.h
- components
- src
- CMakeLists.txt
```

The files under `freertos-configs` are copied from the `freertos/vendors/espressif/boards/esp32/aws_demos/config_files/` and `freertos/demos/include/` directories\. Then, in your top level `CMakeLists.txt` file, add this line before `add_subdirectory(freertos)` so that the compiler will search this directory first\.

```
include_directories(BEFORE freertos-configs)
```

### Providing your own sdkconfig for ESP\-IDF<a name="getting_started_espressif_providing_sdkconfig"></a>

In case you want to provide your own `sdkconfig.default`, you can set the CMake variable `IDF_SDKCONFIG_DEFAULTS`, from the command line:

```
cmake -S . -B build-directory -DIDF_SDKCONFIG_DEFAULTS=path_to_your_sdkconfig_defaults -DCMAKE_TOOLCHAIN_FILE=freertos/tools/cmake/toolchains/xtensa-esp32.cmake -GNinja 
```

If you don't specify a location for your own `sdkconfig.default` file, FreeRTOS uses the default file located at `freertos/vendors/espressif/boards/esp32/aws_demos/sdkconfig.defaults`\.

### Summary<a name="getting_started_espressif_cmake_project_summary"></a>

If you have a project with a component called `example_component`, and you want to override some configurations, here's a complete example of the top level `CMakeLists.txt` file\.

```
cmake_minimum_required(VERSION 3.13)

project(freertos_examples)

add_executable(my_app src/main.c)

# Tell IDF build to link against this target.
set(IDF_PROJECT_EXECUTABLE my_app)

# Add some extra components. IDF_EXTRA_COMPONENT_DIRS is a variable used by ESP-IDF
# to collect extra components.
get_filename_component(
    EXTRA_COMPONENT_DIRS
    "components/example_component" ABSOLUTE
)
list(APPEND IDF_EXTRA_COMPONENT_DIRS ${EXTRA_COMPONENT_DIRS})

# Override the configurations for FreeRTOS.
include_directories(BEFORE freertos-configs)

# Add FreeRTOS as a subdirectory. AFR_BOARD tells which board to target.
set(AFR_BOARD espressif.esp32_devkitc CACHE INTERNAL "")
add_subdirectory(freertos)

# Link against the mqtt library so that we can use it. Dependencies are transitively
# linked.
target_link_libraries(my_app PRIVATE AFR::mqtt)
```

## Troubleshooting<a name="getting_started_espressif_troubleshooting"></a>
+ If you're running macOS and the operating system doesn't recognize your ESP\-WROVER\-KIT, make sure you don't have the D2XX drivers installed\. To uninstall them, follow the instructions in the [ FTDI Drivers Installation Guide for macOS X](http://www.ftdichip.com/Support/Documents/AppNotes/AN_134_FTDI_Drivers_Installation_Guide_for_MAC_OSX.pdf)\.
+ The monitor utility provided by ESP\-IDF \(and invoked using make monitor\) helps you decode addresses\. For this reason, it can help you get some meaningful backtraces in the event the application stops working\. For more information, see [ Automatic Address Decoding](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/api-guides/tools/idf-monitor.html#automatic-address-decoding) on the Espressif website\.
+ It's also possible to enable GDBstub for communication with gdb without requiring any special JTAG hardware\. For more information, see [ Launching GDB with GDBStub](https://docs.espressif.com/projects/esp-idf/en/release-v4.2/esp32/api-guides/tools/idf-monitor.html#launching-gdb-with-gdbstub) on the Espressif website\.
+ For information about setting up an OpenOCD\-based environment if JTAG hardware\-based debugging is required, see [ JTAG Debugging](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-guides/jtag-debugging/index.html) on the Espressif website\.
+ If `pyserial` can't be installed using `pip` on macOS, download it from the [pyserial website](https://pypi.org/simple/pyserial)\.
+ If the board resets continuously, try erasing the flash by entering the following command on the terminal\.

  ```
  make erase_flash
  ```
+ If you see errors when you run `idf_monitor.py`, use Python 2\.7\.
+ Required libraries from ESP\-IDF are included in FreeRTOS, so there is no need to download them externally\. If the `IDF_PATH` environment variable is set, we recommend that you clear it before you build FreeRTOS\.
+ On Windows, it can take 3\-4 minutes for the project to build\. To reduce the build time, you can use the `-j4` switch on the make command\.

  ```
  make flash monitor -j4
  ```
+ If your device has trouble connecting to AWS IoT, open the `aws_clientcredential.h` file, and verify that the configuration variables are properly defined in the file\. `clientcredentialMQTT_BROKER_ENDPOINT[]` should look like `1234567890123-ats.iot.us-east-1.amazonaws.com`\.
+ If you're following the steps in [Using FreeRTOS in your own CMake project for ESP32](#getting_started_espressif_cmake_project) and you see undefined reference errors from the linker, it's usually because of missing dependent libraries or demos\. To add them, update the `CMakeLists.txt` file \(under the root directory\) using the standard CMake function `target_link_libraries`\.
+ ESP\-IDF v4\.2 supports the use of the *xtensa\\\-esp32\\\-elf\\\-gcc 8\\\.2\\\.0\\\.* toolchain\. If you're using an earlier version of the Xtensa toolchain, download the required version\.
+ If you see an error log like the following about python dependencies that are not being met for ESP\-IDF v4\.2:

  ```
  The following Python requirements are not satisfied:
    click>=5.0
    pyserial>=3.0
    future>=0.15.2
    pyparsing>=2.0.3,<2.4.0
    pyelftools>=0.22
    gdbgui==0.13.2.0
    pygdbmi<=0.9.0.2
    reedsolo>=1.5.3,<=1.5.4
    bitstring>=3.1.6
    ecdsa>=0.16.0
    Please follow the instructions found in the "Set up the tools" section of ESP-IDF Getting Started Guide
  ```

  Install the python dependencies on your platform using the following Python command:

  ```
  root/vendors/espressif/esp-idf/requirements.txt
  ```

For more troubleshooting information, see [Troubleshooting getting started](gsg-troubleshooting.md)\.

## Debugging<a name="getting_started_espressif_debugging"></a>

### Debugging code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT \(ESP\-IDF v4\.2\)<a name="debugging-espressif-idf42"></a>

This section shows you how to debug Espressif hardware using ESP\-IDF v4\.2\. You need a JTAG to USB cable\. We use a USB to MPSSE cable \(for example, the [ FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Products/Cables/USBMPSSE.htm)\)\.

**ESP\-DevKitC JTAG setup**  
For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32 DevkitC\.  

```
| C232HM-DDHSL-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name |
| ------------------------- | -------------- | ---------------- |
|  Brown (pin 5)            |  IO14          |  TMS             |
|  Yellow (pin 3)           |  IO12          |  TDI             |
|  Black (pin 10)           |  GND           |  GND             |
|  Orange (pin 2)           |  IO13          |  TCK             |
|  Green (pin 4)            |  IO15          |  TDO             |
```

**ESP\-WROVER\-KIT JTAG setup**  
For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32\-WROVER\-KIT\.  

```
| C232HM-DDHSL-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name |
| ------------------------- | -------------- | ---------------- |
|  Brown (pin 5)            |  IO14          |  TMS             |
|  Yellow (pin 3)           |  IO12          |  TDI             |
|  Orange (pin 2)           |  IO13          |  TCK             |
|  Green (pin 4)            |  IO15          |  TDO             |
```
These tables were developed from the [ FTDI C232HM\-DDHSL\-0 datasheet](https://www.ftdichip.com/Support/Documents/DataSheets/Cables/DS_C232HM_MPSSE_CABLE.pdf)\. For more information, see the section "C232HM MPSSE Cable Connection and Mechanical Details in the data sheet\.  
To enable JTAG on the ESP\-WROVER\-KIT, place jumpers on the TMS, TDO, TDI, TCK, and S\_TDI pins as shown here\.  

![\[Jumper placement\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/JP8-jumpers.png)

**Debugging on Windows \(ESP\-IDF v4\.2\)**  

**To set up for debugging on Windows**

1. Connect the USB side of the FTDI C232HM\-DDHSL\-0 to your computer and the other side as described in [Debugging code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT \(ESP\-IDF v4\.2\)](#debugging-espressif-idf42)\. The FTDI C232HM\-DDHSL\-0 device should appear in **Device Manager** under **Universal Serial Bus Controllers**\.

1. Under the list of universal serial bus devices, right\-click the **C232HM\-DDHSL\-0** device, and then choose **Properties**\.
**Note**  
The device might be listed as **USB Serial Port**\.

   To see the properties of the device, in the properties window, choose the **Details** tab\. If the device isn't listed, install the [Windows driver for FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Drivers/D2XX.htm)\.

1. On the **Details** tab, choose **Property**, and then choose **Hardware IDs**\. You should see something like this in the **Value** field\.

   ```
   FTDIBUS\COMPORT&VID_0403&PID_6014
   ```

   In this example, the vendor ID is 0403 and the product ID is 6014\.

   Verify these IDs match the IDs in `projects/espressif/esp32/make/aws_demos/esp32_devkitj_v1.cfg`\. The IDs are specified in a line that begins with `ftdi_vid_pid` followed by a vendor ID and a product ID\.

   ```
   ftdi_vid_pid 0x0403 0x6014
   ```

1. Download [OpenOCD for Windows](https://github.com/espressif/openocd-esp32/releases)\.

1. Unzip the file to `C:\` and add `C:\openocd-esp32\bin` to your system path\.

1. OpenOCD requires libusb, which is not installed by default on Windows\. To install libusb:

   1. Download [zadig\.exe](https://zadig.akeo.ie)\.

   1. Run `zadig.exe`\. From the **Options** menu, choose **List All Devices**\.

   1. From the dropdown menu, choose **C232HM\-DDHSL\-0**\.

   1. In the target driver field, to the right of the green arrow, choose **WinUSB**\.

   1. For the list under the target driver field, choose the arrow, and then choose **Install Driver**\. Choose **Replace Driver**\.

1. Open a command prompt, navigate to the root of your FreeRTOS download directory, and run the following command\.

   ```
   idf.py openocd
   ```

   Leave this command prompt open\.

1. Open a new command prompt, navigate to the root of your FreeRTOS download directory, and run

   ```
   idf.py flash monitor
   ```

1. Open another command prompt, navigate to the root of your FreeRTOS download directory, and wait until the demo starts running on your board\. When it does, run

   ```
   idf.py gdb
   ```

   The program should stop in the `main` function\.
**Note**  
The ESP32 supports a maximum of two break points\.

**Debugging on macOS \(ESP\-IDF v4\.2\)**  

1. Download the [FTDI driver for macOS](http://www.ftdichip.com/Drivers/VCP.htm)\.

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\. 

1. Extract the downloaded \.tar file and set the path in `.bash_profile` to `OCD_INSTALL_DIR/openocd-esp32/bin`\.

1. Use the following command to install `libusb` on macOS\.

   ```
   brew install libusb
   ```

1. Use the following command to unload the serial port driver\.

   ```
   sudo kextunload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. Use the following command to unload the serial port driver\.

   ```
   sudo kextunload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. If you're running a macOS version later than 10\.9, use the following command to unload the Apple FTDI driver\.

   ```
   sudo kextunload -b com.apple.driver.AppleUSBFTDI
   ```

1. Use the following command to get the product ID and vendor ID of the FTDI cable\. It lists the attached USB devices\.

   ```
   system_profiler SPUSBDataType
   ```

   The output from `system_profiler` should look like the following\.

   ```
      DEVICE:
   
      Product ID: product-ID
      Vendor ID: vendor-ID (Future Technology Devices International Limited)
   ```

1. Open the `projects/espressif/esp32/make/aws_demos/esp32_devkitj_v1.cfg` file\. The vendor ID and product ID for your device are specified in a line that begins with `ftdi_vid_pid`\. Change the IDs to match the IDs from the `system_profiler` output in the previous step\.

1. Open a terminal window, navigate to the root of your FreeRTOS download directory, and use the following command to run OpenOCD\.

   ```
   idf.py openocd
   ```

   Leave this terminal window open\.

1. Open a new terminal, and use the following command to load the FTDI serial port driver\.

   ```
   sudo kextload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. Navigate to the root of your FreeRTOS download directory, and run 

   ```
   idf.py flash monitor
   ```

1. Open another new terminal, navigate to the root of your FreeRTOS download directory, and run

   ```
   idf.py gdb
   ```

   The program should stop at `main`\.

**Debugging on Linux \(ESP\-IDF v4\.2\)**  

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\. Extract the tarball and follow the installation instructions in the readme file\.

1. Use the following command to install libusb on Linux\.

   ```
   sudo apt-get install libusb-1.0
   ```

1. Open a terminal and enter ls \-l /dev/ttyUSB\* to list all USB devices connected to your computer\. This helps you check if the board's USB ports are recognized by the operating system\. You should see output like the following\.

   ```
      $ls -l /dev/ttyUSB*
      crw-rw----    1    root    dialout    188,    0    Jul    10    19:04    /dev/ttyUSB0
      crw-rw----    1    root    dialout    188,    1    Jul    10    19:04    /dev/ttyUSB1
   ```

1. Sign off and then sign in and cycle the power to the board to make the changes take effect\. In a terminal prompt, list the USB devices\. Make sure the group owner has changed from `dialout` to `plugdev`\.

   ```
      $ls -l /dev/ttyUSB*
      crw-rw----    1    root    plugdev    188,    0    Jul    10    19:04    /dev/ttyUSB0
      crw-rw----    1    root    plugdev    188,    1    Jul    10    19:04    /dev/ttyUSB1
   ```

   The `/dev/ttyUSBn` interface with the lower number is used for JTAG communication\. The other interface is routed to the ESP32's serial port \(UART\) and is used for uploading code to the ESP32's flash memory\.

1. In a terminal window, navigate to the root of your FreeRTOS download directory, and use the following command to run OpenOCD\.

   ```
   idf.py openocd 
   ```

1. Open another terminal, navigate to the root of your FreeRTOS download directory, and run the following command\.

   ```
   idf.py flash monitor 
   ```

1. Open another terminal, navigate the root of your FreeRTOS download directory, and run the following command:

   ```
   idf.py gdb
   ```

   The program should stop in `main()`\.
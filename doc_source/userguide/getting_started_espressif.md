# Getting started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT<a name="getting_started_espressif"></a>

This tutorial provides instructions for getting started with the Espressif ESP32\-DevKitC equipped with ESP32\-WROOM\-32, ESP32\-SOLO\-1, or ESP\-WROVER modules and the ESP\-WROVER\-KIT\-VB\. To purchase one from our partner on the AWS Partner Device catalog, use the following links: [ESP32\-WROOM\-32 DevKitC](https://devices.amazonaws.com/detail/a3G0L00000AANtjUAH/ESP32-DevKitC), [ESP32\-SOLO\-1](https://devices.amazonaws.com/detail/a3G0h0000076lSMEAY), or [ ESP32\-WROVER\-KIT](https://devices.amazonaws.com/detail/a3G0L00000AANtlUAH/ESP-WROVER-KIT)\. These versions of development boards are supported on FreeRTOS\. For more information about these boards, see [ ESP32\-DevKitC](https://docs.espressif.com/projects/esp-idf/en/latest/hw-reference/modules-and-boards.html#esp32-devkitc-v4) or [ ESP\-WROVER\-KIT](https://docs.espressif.com/projects/esp-idf/en/latest/hw-reference/modules-and-boards.html#esp-wrover-kit-v4-1) on the Espressif website\.

**Note**  
Currently, the FreeRTOS port for ESP32\-WROVER\-KIT and ESP DevKitC does not support the following features:  
Symmetric multiprocessing \(SMP\)\.

## Overview<a name="getting_started_espressif-overview"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Prerequisites<a name="setup-espressif-prereqs"></a>

Before you get started with FreeRTOS on your Espressif board, you need to set up your AWS account and permissions\.

To create an AWS account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an IAM user to your AWS account, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. To grant your IAM user account access to AWS IoT and FreeRTOS, attach the following IAM policies to your IAM user account:
+ `AmazonFreeRTOSFullAccess`
+ `AWSIoTFullAccess`

**To attach the AmazonFreeRTOSFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To attach the AWSIoTFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM and user accounts, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

For more information about policies, see [IAM Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

## Set up the Espressif hardware<a name="setup-hw-espressif"></a>

See the [ESP32\-DevKitC Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-devkitc.html) for information about setting up the ESP32\-DevKitC development board hardware\.

See the [ESP\-WROVER\-KIT Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-wrover-kit.html) for information about setting up the ESP\-WROVER\-KIT development board hardware\.

**Note**  
Do not proceed to the **Get Started** section of the Espressif guides\. Instead, follow the steps below\.

## Set up your development environment<a name="setup-env-esspressif"></a>

To communicate with your board, you need to download and install a toolchain\.

### Setting up the toolchain<a name="setup-toolchain"></a>

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

### Install CMake<a name="gsg-espressif-cmake"></a>

The CMake build system is required to build the FreeRTOS demo and test applications for this device\. FreeRTOS supports versions 3\.13 and later\.

You can download the latest version of CMake from [CMake\.org](https://cmake.org/download/)\. Both source and binary distributions are available\.

For more details about using CMake with FreeRTOS, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

## Establish a serial connection<a name="establish-serial-connection"></a>

To establish a serial connection between your host machine and the ESP32\-DevKitC, you must install CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\. 

To establish a serial connection between your host machine and the ESP32\-WROVER\-KIT, you must install some FTDI virtual COM port drivers\. You can download these drivers from [FTDI](https://www.ftdichip.com/Drivers/VCP.htm)\.

For more information, see [Establish Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html)\. After you establish a serial connection, make a note of the serial port for your board's connection\. You need it when you build the demo\.

## Download and configure FreeRTOS<a name="download-and-configure-espressif"></a>

After your environment is set up, you can download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos), or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\.

### Configure the FreeRTOS demo applications<a name="config-demos"></a>

1. If you are running macOS or Linux, open a terminal prompt\. If you are running Windows, open mingw32\.exe\. \([MinGW ](https://sourceforge.net/projects/mingw-w64/files/) is a minimalist development environment for native Microsoft Windows applications\.\)

1. To verify that you have Python 2\.7\.10 or later installed, run python \-\-version\. The version installed is displayed\. If you do not have Python 2\.7\.10 or later installed, you can install it from the [Python website](https://www.python.org/downloads/)\.

1. You need the AWS CLI to run AWS IoT commands\. If you are running Windows, use the easy\_install awscli to install the AWS CLI in the mingw32 environment\.

   If you are running macOS or Linux, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Run aws configure and configure the AWS CLI with your AWS access key ID, secret access key, and default region name\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

1. Use the following command to install the AWS SDK for Python \(boto3\):
   + On Windows, in the mingw32 environment, run easy\_install boto3\.
   + On macOS or Linux, run pip install tornado nose \-\-user and then run pip install boto3 \-\-user\.

FreeRTOS includes the `SetupAWS.py` script to make it easier to set up your Espressif board to connect to AWS IoT\. To configure the script, open `freertos/tools/aws_config_quick_start/configure.json` and set the following attributes:

`afr_source_dir`  
The complete path to the *freertos* directory on your computer\. Make sure that you use forward slashes to specify this path\.

`thing_name`  
The name that you want to assign to the AWS IoT thing that represents your board\.

`wifi_ssid`  
The SSID of your Wi\-Fi network\.

`wifi_password`  
The password for your Wi\-Fi network\.

`wifi_security`  
The security type for your Wi\-Fi network\.   
Valid security types are:  
+ `eWiFiSecurityOpen` \(Open, no security\)
+ `eWiFiSecurityWEP` \(WEP security\)
+ `eWiFiSecurityWPA` \(WPA security\)
+ `eWiFiSecurityWPA2` \(WPA2 security\)

**To run the configuration script**

1. If you are running macOS or Linux, open a terminal prompt\. If you are running Windows, open mingw32\.exe\.

1. Go to the `freertos/tools/aws_config_quick_start` directory and run python SetupAWS\.py setup\.

The script does the following:
+ Creates an IoT thing, certificate, and policy
+ Attaches the IoT policy to the certificate and the certificate to the AWS IoT thing
+ Populates the `aws_clientcredential.h` file with your AWS IoT endpoint, Wi\-Fi SSID, and credentials
+ Formats your certificate and private key and writes them to the `aws_clientcredential_keys.h` header file
**Note**  
The certificate is hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

For more information about `SetupAWS.py`, see the README\.md in the `freertos/tools/aws_config_quick_start` directory\.

## Build, flash, and run the FreeRTOS demo project<a name="build-and-run-example-espressif"></a>

You can use CMake to generate the build files, Make to build the application binary, and Espressif's IDF utility to flash your board\.

### Build FreeRTOS on Linux and MacOS<a name="build-espressif"></a>

\(If you are using Windows, please see the next section\.\)

Use CMake to generate the build files, and then use Make to build the application\.

**To generate the demo application's build files with CMake**

1. Change directories to the root of your FreeRTOS download directory\.

1. Use the following command to generate the build files:

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_wrover_kit -DCOMPILER=xtensa-esp32 -S . -B your-build-directory
   ```
**Note**  
If you want to build the application for debugging, add the `-DCMAKE_BUILD_TYPE=Debug` flag to this command\.  
If you want to generate the test application build files, add the `-DAFR_ENABLE_TESTS=1` flag\.  
The code provided by Espressif uses the lightweight IP \(lwIP\) stack as the default networking stack\. To use the FreeRTOS\+TCP networking stack instead, add the `–DAFR_ESP_FREERTOS_TCP` flag to the CMake command\.  
To add the lwIP dependency for non\-vendor provided code, add the following lines to the CMake dependency file, `CMakeLists.txt`, for your custom WiFi component\.  

   ```
   # Add a dependency on the bluetooth espressif component to the common component
   set(COMPONENT_REQUIRES lwip)
   ```

**To build the application with make**

1. Change directories to the `build` directory\.

1. Use the following command to build the application with Make:

   ```
   make all -j4
   ```
**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

### Build FreeRTOS on Windows<a name="build-espressif-windows"></a>

On Windows, you must specify a build generator for CMake, otherwise CMake defaults to Visual Studio\. Espressif officially recommends the Ninja build system because it works on Windows, Linux and MacOS\. You must run CMake commands in a native Windows environment like cmd or PowerShell\. Running CMake commands in a virtual Linux environment, like MSYS2 or WSL, is not supported\.

Use CMake to generate the build files, and then use Make to build the application\.

**To generate the demo application's build files with CMake**

1. Change directories to the root of your FreeRTOS download directory\.

1. Use the following command to generate the build files:

   ```
   cmake -DVENDOR=espressif -DBOARD=esp32_wrover_kit -DCOMPILER=xtensa-esp32 -GNinja -S . -B build-directory
   ```
**Note**  
If you want to build the application for debugging, add the `-DCMAKE_BUILD_TYPE=Debug` flag to this command\.  
If you want to generate the test application build files, add the `-DAFR_ENABLE_TESTS=1` flag\.  
The code provided by Espressif uses the lightweight IP \(lwIP\) stack as the default networking stack\. To use the FreeRTOS\+TCP networking stack instead, add the `–DAFR_ESP_FREERTOS_TCP` flag to the CMake command\.  
To add the lwIP dependency for non\-vendor provided code, add the following lines to the CMake dependency file, `CMakeLists.txt`, for your custom WiFi component\.  

   ```
   # Add a dependency on the bluetooth espressif component to the common component
   set(COMPONENT_REQUIRES lwip)
   ```

**To build the application**

1. Change directories to the `build` directory\.

1. Invoke Ninja to build the application:

   ```
   ninja
   ```

   Or, use the generic CMake interface to build the application:

   ```
   cmake --build build-directory
   ```
**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

### Flash and run FreeRTOS<a name="flash-espressif"></a>

Use Espressif's IDF utility \(`freertos/vendors/espressif/esp-idf/tools/idf.py`\) to flash your board, run the application, and see logs\.

To erase the board's flash, go to the `freertos` directory and use the following command:

```
./vendors/espressif/esp-idf/tools/idf.py erase_flash -B build-directory
```

To flash the application binary to your board, use `make`:

```
make flash
```

You can also use the IDF script to flash your board:

```
./vendors/espressif/esp-idf/tools/idf.py flash -B build-directory
```

To monitor:

```
./vendors/espressif/esp-idf/tools/idf.py monitor -p /dev/ttyUSB1 -B build-directory
```

**Note**  
You can combine these commands\. For example:  

```
./vendors/espressif/esp-idf/tools/idf.py erase_flash flash monitor -p /dev/ttyUSB1 -B build-directory
```

### Monitoring MQTT messages on the cloud<a name="gsg-espressif-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

### Run the Bluetooth Low Energy demos<a name="espressif-run-ble"></a>

FreeRTOS supports [Bluetooth Low Energy](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-library.html) connectivity\.

To run the FreeRTOS demo project across Bluetooth Low Energy, you need to run the FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application on an iOS or Android mobile device\.

**To set up the FreeRTOS Bluetooth Low Energy mobile SDK demo application**

1. Follow the instructions in [Mobile SDKs for FreeRTOS Bluetooth Devices](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-mobile.html) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-sdk-app) to set up the demo mobile application on your mobile device\.

For instructions about how to run the MQTT over Bluetooth Low Energy demo on your board, see the [MQTT over Bluetooth Low Energy Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt)\.

For instructions about how to run the Wi\-Fi provisioning demo on your board, see the [Wi\-Fi Provisioning Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-wifi)\.

## Using FreeRTOS in your own CMake project for ESP32<a name="getting_started_espressif_cmake_project"></a>

If you want to consume FreeRTOS in your own CMake project, you can set it up as a subdirectory and build it together with your application\. First, get a copy of FreeRTOS either from [GitHub](https://github.com/aws/amazon-freertos), or from the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. If you're using git, you can also set it up as a git submodule with the following command so it's easier to update in the future\. 

```
git submodule add -b release https://github.com/aws/amazon-freertos.git freertos
```

If a newer version is released, you can update your local copy with these commands\.

```
# Pull the latest changes from the remote tracking branch.
git submodule update --remote -- amazon-freertos
# Commit the submodule change because it is pointing to a different revision now.
git add amazon-freertos
git commit -m "Update FreeRTOS to a new release"
```

Assuming your project has the following directory structure:

```
- freertos (the copy that you obtained from GitHub or the AWS IoT console)
- src
  - main.c (your application code)
- CMakeLists.txt
```

Here's an example of the top\-level `CMakeLists.txt` file that can be used to build your application together with FreeRTOS\.

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
cmake --build build
```

To flash the application to your board, run

```
cmake --build build-directory --target flash
```

### Using components from FreeRTOS<a name="getting_started_espressif_cmake_project_components"></a>

After running CMake, you can find all available components in the summary output\. It should look something like this:

```
====================Configuration for FreeRTOS====================
  Version:                 201910.00
  Git version:             201910.00-388-gcb3612cb7

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
  Modules to build:        ble, ble_hal, ble_wifi_provisioning, common, crypto, defender,
                           dev_mode_key_provisioning, freertos_plus_tcp, greengrass,
                           https, kernel, mqtt, ota, pkcs11, pkcs11_implementation,
                           platform, secure_sockets, serializer, shadow, tls, wifi
  Enabled by user:         ble, ble_hal, ble_wifi_provisioning, defender, greengrass,
                           https, mqtt, ota, pkcs11, pkcs11_implementation, platform,
                           secure_sockets, shadow, wifi
  Enabled by dependency:   common, crypto, demo_base, dev_mode_key_provisioning,
                           freertos, freertos_plus_tcp, kernel, pkcs11_mbedtls,
                           secure_sockets_freertos_plus_tcp, serializer, tls, utils
  3rdparty dependencies:   http_parser, jsmn, mbedtls, pkcs11, tinycbor
  Available demos:         demo_ble, demo_ble_numeric_comparison, demo_defender,
                           demo_greengrass_connectivity, demo_https, demo_mqtt, demo_ota,
                           demo_shadow, demo_tcp, demo_wifi_provisioning
  Available tests:
=========================================================================
```

You can reference any components from the "Modules to build" list\. To link them into your application, put the `AFR::` namespace in front of the name, for example, `AFR::mqtt`, `AFR::ota`, etc\.

### Add custom components to ESP\-IDF<a name="getting_started_espressif_cmake_project_espidf"></a>

You can add more components to the ESP\-IDF build environment\. For example, assuming you want to add a component called `foo`, and your project looks like this:

```
- freertos
- components
  - foo
    - include
      - foo.h
    - src
      - foo.c
    - CMakeLists.txt
- src
  - main.c
- CMakeLists.txt
```

Here's an example of the CMakeLists\.txt file for your component:

```
# include paths of this components.
set(COMPONENT_ADD_INCLUDEDIRS include)

# source files of this components.
set(COMPONENT_SRCDIRS src)
# Alternatively, use COMPONENT_SRCS to specify source files explicitly
# set(COMPONENT_SRCS src/foo.c)

# add this components, this will define a CMake library target.
register_component()
```

You can also specify dependencies using the standard CMake function `target_link_libraries`\. Note that the target name for your component is stored in the variable `COMPONENT_TARGET`, defined by the ESP\-IDF\.

```
# add this component, this will define a CMake library target.
register_component()

# standard CMake function can be used to specify dependencies. ${COMPONENT_TARGET} is defined
# from esp-idf when you call register_component, by default it's idf_component_<folder_name>.
target_link_libraries(${COMPONENT_TARGET} PRIVATE AFR::mqtt)
```

For ESP components, this is done by setting 2 variables `COMPONENT_REQUIRES` and `COMPONENT_PRIV_REQUIRES`\. See [ Build System \(CMake\)](https://docs.espressif.com/projects/esp-idf/en/v3.3/api-guides/build-system-cmake.html) in the *ESP\-IDF Programming Guide v3\.3*\.

```
# If the dependencies are from ESP-IDF, use these 2 variables. Note these need to be
# set before calling register_component().
set(COMPONENT_REQUIRES log)
set(COMPONENT_PRIV_REQUIRES lwip)
```

Then, in the top level `CMakeLists.txt` file, you tell ESP\-IDF where to find these components\. Insert the following lines anywhere before `add_subdirectory(freertos)`:

```
# Add some extra components. IDF_EXTRA_COMPONENT_DIRS is a variable used by ESP-IDF
# to collect extra components.
get_filename_component(
    EXTRA_COMPONENT_DIRS
    "components/foo" ABSOLUTE
)
list(APPEND IDF_EXTRA_COMPONENT_DIRS ${EXTRA_COMPONENT_DIRS})
```

This component is now automatically linked to your application code by default\. You should be able to include its header files and call the functions it defines\.

### Override the configurations for FreeRTOS<a name="getting_started_espressif_cmake_project_override"></a>

There's currently no well\-defined approach to redefining the configs outside of the FreeRTOS source tree\. By default, CMake will look for the `freertos/vendors/espressif/boards/esp32/aws_demos/config_files/` and `freertos/demos/include/` directories\. However, you can use a workaround to tell the compiler to search other directories first\. For example, you can add another folder for FreeRTOS configurations:

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

The files under `freertos-configs` are copied from the `freertos/vendors/espressif/boards/esp32/aws_demos/config_files/` and `freertos/demos/include/i` directories\. Then, in your top level `CMakeLists.txt` file, add this line before `add_subdirectory(freertos)` so that the compiler will search this directory first:

```
include_directories(BEFORE freertos-configs)
```

### Providing your own sdkconfig for ESP\-IDF<a name="getting_started_espressif_providing_sdkconfig"></a>

In case you want to provide your own `sdkconfig.default`, you can set the CMake variable `IDF_SDKCONFIG_DEFAULTS`, from the command line:

```
cmake -S . -B build-directory -DIDF_SDKCONFIG_DEFAULTS=path_to_your_sdkconfig_defaults -DCMAKE_TOOLCHAIN_FILE=freertos/tools/cmake/toolchains/xtensa-esp32.cmake -GNinja
```

If you don’t specify a location for your own `sdkconfig.default` file, FreeRTOS will use the default file located at `freertos/vendors/espressif/boards/esp32/aws_demos/sdkconfig.defaults`\. 

### Summary<a name="getting_started_espressif_cmake_project_summary"></a>

If you have a project with a component called `foo`, and you want to override some configurations, here's a complete example of the top level `CMakeLists.txt` file\.

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
    "components/foo" ABSOLUTE
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
+ If you are running macOS and the operating system does not recognize your ESP\-WROVER\-KIT, make sure you do not have the D2XX drivers installed\. To uninstall them, follow the instructions in the [ FTDI Drivers Installation Guide for macOS X](http://www.ftdichip.com/Support/Documents/AppNotes/AN_134_FTDI_Drivers_Installation_Guide_for_MAC_OSX.pdf)\.
+ The monitor utility provided by ESP\-IDF \(and invoked using make monitor\) helps you decode addresses\. For this reason, it can help you get some meaningful backtraces in the event the application crashes\. For more information, see [ Automatically Decoding Addresses](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/idf-monitor.html#automatically-decoding-addresses) on the Espressif website\.
+ It is also possible to enable GDBstub for communication with gdb without requiring any special JTAG hardware\. For more information, see [ Launch GDB for GDBStub](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/idf-monitor.html#launch-gdb-for-gdbstub) on the Espressif website\.
+ For information about setting up an OpenOCD\-based environment if JTAG hardware\-based debugging is required, see the document *JTAG Debugging for ESP32* available on the [ Espressif website](https://www.espressif.com/en/support/download/documents?keys=jtag)\.
+ If `pyserial` cannot be installed using `pip` on macOS, download it from the [pyserial website](https://pypi.org/simple/pyserial)\.
+ If the board resets continuously, try erasing the flash by entering the following command on the terminal:

  ```
  make erase_flash
  ```
+ If you see errors when you run `idf_monitor.py`, use Python 2\.7\.
+ Required libraries from ESP\-IDF are included in FreeRTOS, so there is no need to download them externally\. If the `IDF_PATH` environment variable is set, we recommend that you clear it before you build FreeRTOS\. 
+ On Windows, it can take 3\-4 minutes for the project to build\. You can use the `-j4` switch on the make command to reduce the build time:

  ```
  make flash monitor -j4
  ```
+ If your device has trouble connecting to AWS IoT, open the `aws_clientcredential.h` file, and verify that the configuration variables are properly defined in the file\. `clientcredentialMQTT_BROKER_ENDPOINT[]` should look like `1234567890123-ats.iot.us-east-1.amazonaws.com`\. 
+ If you're following the steps in [Using FreeRTOS in your own CMake project for ESP32](#getting_started_espressif_cmake_project) and you see undefined reference errors from the linker, it's usually because of missing dependent libraries or demos\. To add them, update the `CMakeLists.txt` file \(under the root directory\) using the standard CMake function `target_link_libraries`\.

For troubleshooting information, see [Troubleshooting getting started](gsg-troubleshooting.md)\.

### Debugging code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT<a name="debugging-espressif"></a>

You need a JTAG to USB cable\. We use a USB to MPSSE cable \(for example, the [FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Products/Cables/USBMPSSE.htm)\)\. 

#### ESP\-DevKitC JTAG setup<a name="jtag-devkitc"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32 DevkitC:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  | TMS | 
|  Yellow \(pin 3\)  |  IO12  | TDI | 
| Black \(pin 10\) | GND | GND | 
| Orange \(pin 2\) | IO13 | TCK | 
| Green \(pin 4\) | IO15 | TDO | 

#### ESP\-WROVER\-KIT JTAG setup<a name="jtag-wrover"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32\-WROVER\-KIT:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  |  TMS  | 
|  Yellow \(pin 3\)  |  IO12  |  TDI  | 
|  Orange \(pin 2\)  |  IO13  |  TCK  | 
|  Green \(pin 4\)  |  IO15  |  TDO  | 

These tables were developed from the [ FTDI C232HM\-DDHSL\-0 datasheet](http://www.ftdichip.com/Support/Documents/DataSheets/Cables/DS_C232HM_MPSSE_CABLE.PDF)\. For more information, see C232HM MPSSE Cable Connection and Mechanical Details in the datasheet\.

To enable JTAG on the ESP\-WROVER\-KIT, place jumpers on the TMS, TDO, TDI, TCK, and S\_TDI pins as shown here:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/JP8-jumpers.png)

#### Debugging on Windows<a name="debugging-espressif-windows"></a>

**To set up for debugging on Windows**

1. Connect the USB side of the FTDI C232HM\-DDHSL\-0 to your computer and the other side as described in [Debugging code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT](#debugging-espressif)\. The FTDI C232HM\-DDHSL\-0 device should appear in **Device Manager** under **Universal Serial Bus Controllers**\.

1. Under the list of universal serial bus devices, right\-click the **C232HM\-DDHSL\-0** device, and choose **Properties**\.
**Note**  
The device might be listed as **USB Serial Port**\.

   In the properties window, choose the **Details** tab to see the properties of the device\. If the device is not listed, install the [Windows driver for FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Drivers/D2XX.htm)\.

1. On the **Details** tab, choose **Property**, and then choose **Hardware IDs**\. You should see something like this in the **Value** field:

   ```
   FTDIBUS\COMPORT&VID_0403&PID_6014
   ```

   In this example, the vendor ID is 0403 and the product ID is 6014\.

   Verify these IDs match the IDs in `projects/espressif/esp32/make/aws_demos/esp32_devkitj_v1.cfg`\. The IDs are specified in a line that begins with `ftdi_vid_pid` followed by a vendor ID and a product ID:

   ```
   ftdi_vid_pid 0x0403 0x6014
   ```

1. Download [OpenOCD for Windows](https://github.com/espressif/openocd-esp32/releases)\.

1. Unzip the file to `C:\` and add `C:\openocd-esp32\bin` to your system path\.

1. OpenOCD requires libusb, which is not installed by default on Windows\.

   To install libusb

   1. Download [zadig\.exe](https://zadig.akeo.ie)\.

   1. Run `zadig.exe`\. From the **Options** menu, choose **List All Devices**\.

   1. From the drop\-down menu, choose **C232HM\-DDHSL\-0**\.

   1. In the target driver field, to the right of the green arrow, choose **WinUSB**\.

   1. From the drop\-down box under the target driver field, choose the arrow, and then choose **Install Driver**\. Choose **Replace Driver**\.

1. Open a command prompt, navigate to `projects/espressif/esp32/make/aws_demos` and run:

   *For ESP32\-WROOM\-32 and ESP32\-WROVER:*

   ```
   openocd.exe -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

   *For ESP32\-SOLO\-1:*

   ```
   openocd.exe -f esp32_devkitj_v1.cfg -f esp-solo-1.cfg
   ```

   Leave this command prompt open\.

1. Open a new command prompt, navigate to your `msys32` directory, and run `mingw32.exe`\. In the mingw32 terminal, navigate to `projects/espressif/esp32/make/aws_demos` and run make flash monitor\.

1. Open another mingw32 terminal, navigate to `projects/espressif/esp32/make/aws_demos` and wait until the demo starts running on your board\. When it does, run `xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf`\. The program should stop in the `main` function\.

**Note**  
The ESP32 supports a maximum of two break points\.

#### Debugging on macOS<a name="debugging-espressif-macos"></a>

1. Download the [FTDI driver for macOS](http://www.ftdichip.com/Drivers/VCP.htm)\.

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\.

1. Extract the downloaded \.tar file and set the path in `.bash_profile` to `OCD_INSTALL_DIR/openocd-esp32/bin`\.

1. Use the following command to install `libusb` on macOS:

   ```
   brew install libusb
   ```

1. Use the following command to unload the serial port driver:

   ```
   sudo kextunload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. If you are running a macOS version later than 10\.9, use the following command to unload the Apple FTDI driver:

   ```
   sudo kextunload -b com.apple.driver.AppleUSBFTDI
   ```

1. Use the following command to get the product ID and vendor ID of the FTDI cable\. It lists the attached USB devices:

   ```
   system_profiler SPUSBDataType
   ```

   The output from `system_profiler` should look like this:

   ```
   DEVICE:
                               
   Product ID: product-ID
   Vendor ID: vendor-ID (Future Technology Devices International Limited)
   ```

1. Open `projects/espressif/esp32/make/aws_demos/esp32_devkitj_v1.cfg`\. The vendor ID and product ID for your device are specified in a line that begins with `ftdi_vid_pid`\. Change the IDs to match the IDs from the `system_profiler` output in the previous step\.

1. Open a terminal window, navigate to `projects/espressif/esp32/make/aws_demos`, and use the following command to run OpenOCD\.

   *For ESP32\-WROOM\-32 and ESP32\-WROVER:*

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

   *For ESP32\-SOLO\-1:*

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-solo-1.cfg
   ```

1. Open a new terminal, and use the following command to load the FTDI serial port driver:

   ```
   sudo kextload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. Navigate to `projects/espressif/esp32/make/aws_demos`, and run the following command:

   ```
   make flash monitor
   ```

1. Open another new terminal, navigate to `projects/espressif/esp32/make/aws_demos`, and run the following command:

   ```
   xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf
   ```

   The program should stop at `main()`\.

#### Debugging on Linux<a name="debugging-espressif-linux"></a>

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\. Extract the tarball and follow the installation instructions in the readme file\.

1. Use the following command to install libusb on Linux:

   ```
   sudo apt-get install libusb-1.0
   ```

1. Open a terminal and enter **ls \-l /dev/ttyUSB\*** to list all USB devices connected to your computer\. This helps you check if the board’s USB ports are recognized by the operating system\. You should see output like this:

   ```
   $ls -l /dev/ttyUSB*
   crw-rw----    1    root    dialout    188,    0    Jul    10    19:04    /dev/ttyUSB0
   crw-rw----    1    root    dialout    188,    1    Jul    10    19:04    /dev/ttyUSB1
   ```

1. Sign off and then sign in and cycle the power to the board to make the changes take effect\. In a terminal prompt, list the USB devices\. Make sure the group owner has changed from `dialout` to `plugdev`:

   ```
   $ls -l /dev/ttyUSB*
   crw-rw----    1    root    plugdev    188,    0    Jul    10    19:04    /dev/ttyUSB0
   crw-rw----    1    root    plugdev    188,    1    Jul    10    19:04    /dev/ttyUSB1
   ```

   The `/dev/ttyUSBn` interface with the lower number is used for JTAG communication\. The other interface is routed to the ESP32’s serial port \(UART\) and is used for uploading code to the ESP32’s flash memory\.

1. In a terminal window, navigate to `projects/espressif/esp32/make/aws_demos`, and use the following command to run OpenOCD\.

   *For ESP32\-WROOM\-32 and ESP32\-WROVER:*

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

   *For ESP32\-SOLO\-1:*

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-solo-1.cfg
   ```

1. Open another terminal, navigate to `projects/espressif/esp32/make/aws_demos`, and run the following command:

   ```
   make flash monitor
   ```

1. Open another terminal, navigate to `projects/espressif/esp32/make/aws_demos`, and run the following command:

   ```
   xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf
   ```

   The program should stop in `main()`\.
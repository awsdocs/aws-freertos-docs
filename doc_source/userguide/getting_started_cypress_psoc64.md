# Getting started with the Cypress CY8CKIT\-064S0S2\-4343W kit<a name="getting_started_cypress_psoc64"></a>

This tutorial provides instructions for getting started with the [CY8CKIT\-064S0S2\-4343W](https://www.cypress.com/CY8CKIT-064S0S2-4343W) kit\. If you don't already have one, you can use that link to purchase a kit\. You can also use that link to access the kit user guide\. 

## Getting started<a name="getting_started_cypress_psoc64_first"></a>

Before you begin, you must configure AWS IoT and FreeRTOS to connect your device to the AWS Cloud\. For instructions, see [First steps](freertos-prereqs.md)\. After you complete the prerequisites, you will have a FreeRTOS package with AWS IoT Core credentials\.

**Note**  
In this tutorial, the path to the FreeRTOS download directory created in the "First steps" section is referred to as `freertos`\.

## Setting up the development environment<a name="getting_started_cypress_psoc64_setup"></a>

FreeRTOS works with either a CMake or Make build flow\. You can use ModusToolbox for your Make build flow\. You can use the Eclipse IDE delivered with ModusToolbox or a partner IDE such as IAR EW\-Arm, Arm MDK, or Microsoft Visual Studio Code\. The Eclipse IDE is compatible with the Windows, macOS, and Linux operating systems\. 

Before you begin, download and install the latest [ModusToolbox software](https://www.cypress.com/products/modustoolbox-software-environment)\. For more information, see the [ModusToolbox Installation Guide](https://www.cypress.com/ModusToolboxInstallGuide)\.

### Updating tools for ModusToolbox 2\.1 or older<a name="getting_started_cypress_psoc64_modus_toolbox_updates"></a>

If you're using the ModusToolbox 2\.1 Eclipse IDE to program this kit, you'll need to update the OpenOCD and Firmware\-loader tools\. 

In the following steps, by default the `ModusToolbox` path for:
+ Windows is `C:\Users\user_name\ModusToolbox`\.
+ Linux is `user_home/ModusToolbox` or where you choose to extract the archive file\.
+ MacOS is under the Applications folder in the volume you select in the wizard\.

#### Updating OpenOCD<a name="getting_started_cypress_psoc64_openocd_update"></a>

This kit requires Cypress OpenOCD 4\.0\.0 or later to successfully erase and program the chip\.

**To update Cypress OpenOCD**

1. Go to the [Cypress OpenOCD release page](https://github.com/cypresssemiconductorco/openocd/releases)\.

1. Download the archive file for your OS \(Windows/Mac/Linux\)\.

1. Delete the existing files in `ModusToolbox/tools_2.x/openocd`\.

1. Replace the files in `ModusToolbox/tools_2.x/openocd` with the extracted contents of the archive that you downloaded in a previous step\.

#### Updating Firmware\-loader<a name="getting_started_cypress_psoc64_firmware_loader_update"></a>

This kit requires Cypress Firmware\-loader 3\.0\.0 or later\.

**To update Cypress Firmware\-loader**

1. Go to the [Cypress Firmware\-loader release page](https://github.com/cypresssemiconductorco/Firmware-loader/releases)\.

1. Download the archive file for your OS \(Windows/Mac/Linux\)\.

1. Delete the existing files in `ModusToolbox/tools_2.x/fw-loader`\.

1. Replace the files in `ModusToolbox/tools_2.x/fw-loader` with the extracted contents of the archive that you downloaded in a previous step\.

Alternatively, you can use CMake to generate project build files from FreeRTOS application source code, build the project using your preferred build tool, and then program the kit using OpenOCD\. If you prefer to use a GUI tool for programming with the CMake flow, download and install Cypress Programmer from the [Cypress Programming Solutions](https://www.cypress.com/products/psoc-programming-solutions) webpage\. For more information, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

## Setting up your hardware<a name="getting_started_cypress_psoc64_hardware"></a>

Follow these steps to set up the kit's hardware\.

1. 

**Provision your kit**  
Follow the [Provisioning Guide for CY8CKIT\-064S0S2\-4343W Kit](https://community.cypress.com/docs/DOC-20043) instructions to securely provision your kit for AWS IoT\.

   This kit requires CySecureTools 3\.1\.0 or later\.

1. **Set up a serial connection**

   1. Connect the kit to your host computer\.

   1. The USB Serial port for the kit is automatically enumerated on the host computer\. Identify the port number\. In Windows, you can identify it using the **Device Manager** under **Ports** \(COM & LPT\)\.

   1. Start a serial terminal and open a connection with the following settings:
      + Baud rate: 115200
      + Data: 8 bit
      + Parity: None
      + Stop bits: 1
      + Flow control: None

## Build and run the FreeRTOS Demo project<a name="getting_started_cypress_psoc64_build_run"></a>

In this section you build and run the demo\.

1. Make sure to follow the steps in [Provisioning Guide for CY8CKIT\-064S0S2\-4343W Kit](https://community.cypress.com/docs/DOC-20043)\.

1. **Build the FreeRTOS Demo\.**

   1. Open the Eclipse IDE for ModusToolbox and choose, or create, a workspace\.

   1. From the **File** menu, choose **Import**\.

      Expand **General**, choose **Existing Project Into Workspace**, and then choose **Next**\.

   1. In **Root Directory**, enter  `freertos/projects/cypress/CY8CKIT-064S0S2-4343W/mtb/aws_demos` and then select the project name `aws_demos`\. It should be selected by default\.

   1. Choose **Finish** to import the project into your workspace\.

   1. Build the application by doing one of the following:
      + From the **Quick Panel**, select **Build aws\_demos Application**\.
      + Choose **Project** and choose **Build All**\.

      Make sure the project compiles without errors\.

1. 

**Monitoring MQTT Messages on the Cloud**  
Before you run the demo, you can set up the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\. To subscribe to the MQTT topic with the AWS IoT MQTT client, follow these steps\.

   1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

   1. In the navigation pane, choose **Test**, then choose **MQTT test client** to open the MQTT client\.

   1. For **Subscription topic**,  enter ***your\-thing\-name*/example/topic**, and then choose **Subscribe to topic**\.

1. **Run the FreeRTOS demo project**

   1. Select the project `aws_demos` in the workspace\.

   1. From the **Quick Panel**, select **aws\_demos Program \(KitProg3\)**\. This programs the board and the demo application starts running after the programming is finished\.

   1. You can view the status of the running application in the serial terminal\. The following figure shows a part of the terminal output\.  
![\[Screenshot of the terminal output after you build the aws_demo project.\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/gsg-psoc64-terminal-output.png)

      The MQTT demo publishes messages on four different topics \(`iotdemo/topic/n`, where n=1 to 4\) and subscribes to all those topics to receive the same messages back\. When a message is received, the demo publishes an acknowledgement message on the topic `iotdemo/acknowledgements`\. The following list describes the debug messages that appear in the terminal output, with references to the serial numbers of the messages\. In the output, the WICED Host Driver \(WHD\) driver details are printed first without serial numbering\. 

      1. 1 to 4 – Device connects to the configured Access Point \(AP\) and is provisioned by connecting to the AWS server using the configured endpoint and certificates\.

      1. 5 to 13 – coreMQTT library is initialized and device establishes MQTT connection\.

      1. 14 to 17 – Device subscribes to all the topics to receive the published messages back\.

      1. 18 to 30 – Device publishes two messages and waits to receive them back\. When each message is received, the device sends an acknowledgement message\.

      The same cycle of publish, receive, and acknowledge continues until all the messages are published\. Two messages are published per cycle until the number of cycles configured are completed\.

1. 

**Using CMake with FreeRTOS**  
You can also use CMake to build and run the demo application\. To set up CMake and a native build system, see [Prerequisites](getting-started-cmake.md#building-cmake-prereqs)\.

   1. Use the following command to generate build files\. Specify the target board with the `-DBOARD` option\. 

      ```
      cmake -DVENDOR=cypress -DBOARD=CY8CKIT_064S0S2_4343W -DCOMPILER=arm-gcc -S freertos -B build_dir
      ```

      If you're using Windows, you must specify the native build system using the `-G` option because CMake uses Visual Studio by default\.   
**Example**  

      ```
      cmake -DVENDOR=cypress -DBOARD=CY8CKIT_064S0S2_4343W -DCOMPILER=arm-gcc -S freertos -B build_dir -G Ninja
      ```

      If `arm-none-eabi-gcc` is not in your shell path, you also need to set the `AFR_TOOLCHAIN_PATH` CMake variable\.   
**Example**  

      ```
       -DAFR_TOOLCHAIN_PATH=/home/user/opt/gcc-arm-none-eabi/bin
      ```

   1. Use the following command to build the project using CMake\.

      ```
      cmake --build build_dir
      ```

   1. Finally, program the `cm0.hex` and `cm4.hex` files generated under `build_dir` by using Cypress Programmer\.

### Running other demos<a name="getting_started_cypress_psoc64_demos"></a>

The following demo applications have been tested and verified to work with the current release\. You can find these demos under the `freertos/demos` directory\. For information on how to run these demos, see [FreeRTOS demos](freertos-next-steps.md)\.
+ Bluetooth Low Energy demo
+ Over\-the\-Air Updates demo
+ Secure Sockets Echo Client demo
+ AWS IoT Device Shadow demo

## Debugging<a name="getting_started_cypress_psoc64_debugging"></a>

The KitProg3 on the kit supports debugging over the SWD protocol\.
+ To debug the FreeRTOS application, select the **aws\_demos project** in the workspace and then select **aws\_demos Debug \(KitProg3\)** from the **Quick Panel**\.

## OTA updates<a name="getting_started_cypress_psoc64_ota_updates"></a>

PSoC 64 MCUs have passed all of the required FreeRTOS qualification tests\. However, the optional over\-the\-air \(OTA\) feature implemented in the PSoC 64 Standard Secure AWS firmware library is still pending evaluation\. The OTA feature as\-implemented currently passes all of the OTA qualification tests except [ aws\_ota\_test\_case\_rollback\_if\_unable\_to\_connect\_after\_update\.py](https://github.com/aws/amazon-freertos/blob/202012.00/tools/ota_e2e_tests/aws_ota_test/aws_ota_test_case_rollback_if_unable_to_connect_after_update.py)\. 

When a successfully validated OTA image is applied to a device using the PSoC64 Standard Secure – AWS MCU and the device can't communicate with AWS IoT Core, the device can't automatically rollback to the original known good image\. This might result in the device being unreachable from AWS IoT Core for further updates\. This functionality is still under development by the Cypress team\.

For more information, see [OTA Updates with AWS and the CY8CKIT\-064S0S2\-4343W Kit](https://community.cypress.com/docs/DOC-20063)\. If you have further questions or need technical support, contact the [Cypress Developer Community](https://community.cypress.com)\.
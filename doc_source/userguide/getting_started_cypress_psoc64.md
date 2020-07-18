# Getting started with the Cypress CY8CKIT\-064S0S2\-4343W kit<a name="getting_started_cypress_psoc64"></a>

This tutorial provides instructions for getting started with the [CY8CKIT\-064S0S2\-4343W](https://www.cypress.com/CY8CKIT-064S0S2-4343W) kit\. If you don't have a kit, you can purchase one using the previous link\. If you haven't already, sign in to the Cypress website\.

## Getting started<a name="getting_started_cypress_psoc64_first"></a>

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. For instructions, see [First steps](freertos-prereqs.md)\. After you complete the prerequisites, you will have a FreeRTOS package with AWS IoT Core credentials\.

**Note**  
In this tutorial, the path to the FreeRTOS download directory is referred to as *freertos*\.

## Setting up the development environment<a name="getting_started_cypress_psoc64_setup"></a>

FreeRTOS works with either a Cmake or make build flow\. You can use ModusToolbox for your make build flow\. You can use the Eclipse IDE delivered with ModusToolbox or a partner IDE such as IAR EW\-Arm, Arm MDK, or Microsoft Visual Studio Code\. The Eclipse IDE is compatible with the Windows, MacOS, and Linux operating systems\. 

Before you begin, download and install the latest [ModusToolbox software](https://www.cypress.com/products/modustoolbox-software-environment)\. For more information, see the [ModusToolbox Installation Guide](https://www.cypress.com/ModusToolboxInstallGuide)\.

You can also use CMake to generate project build files from FreeRTOS application source code, build the project using your preferred build tool, and then program the kit using OpenOCD\. If you prefer to use a GUI tool for programming, download and install Cypress Programmer from the [Cypress Programming Solutions](https://www.cypress.com/products/psoc-programming-solutions) website\. For more information, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

## Setting up your hardware<a name="getting_started_cypress_psoc64_hardware"></a>

Follow these steps to set up the kit's hardware\.

1. 

**Provision your kit**  
Follow the [Provisioning Guide for CY8CKIT\-064S0S2\-4343W Kit](https://community.cypress.com/docs/DOC-20043) instructions to securely provision your kit for AWS IoT\.

1. 

**Set up a serial connection**  
Follow these steps to set up a serial connection\.

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

1. 

**Build the FreeRTOS Demo\.**  
Follow these steps to build the FreeRTOS demo\.

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

**Run the FreeRTOS demo project**  
Follow these steps to run the FreeRTOS demo\.

   1. Select the project `aws_demos` in the workspace\.

   1. From the **Quick Panel**, select **aws\_demos Program \(KitProg3\)**\. This programs the board and the demo application starts running after the programming is finished\.

   1. You can view the status of the running application in the serial terminal\. The following figure shows a part of the terminal output\.  
![\[Screenshot of the terminal output after you build the aws_demo project.\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/gsg-psoc64-terminal-output.png)

      The MQTT demo publishes messages on four different topics \(`iotdemo/topic/n`, where n=1 to 4\) and subscribes to all those topics to receive the same messages back\. When a message is received, the demo publishes an acknowledgement message on the topic `iotdemo/acknowledgements`\. The following list describes the debug messages that appear in the terminal output, with references to the serial numbers of the messages\. In the output, the WICED Host Driver \(WHD\) driver details are printed first without serial numbering\. 

      1. 1 to 4 – Device connects to the configured Access Point \(AP\) and is provisioned by connecting to the AWS server using the configured endpoint and certificates\.

      1. 5 to 13 – MQTT library is initialized and device establishes MQTT connection\.

      1. 14 to 17 – Device subscribes to all the topics to receive the published messages back\.

      1. 18 to 30 – Device publishes two messages and waits to receive them back\. When each message is received, the device sends an acknowledgement message\.

      The same cycle of publish, receive, and acknowledge continues until all the messages are published\. Two messages are published per cycle until the number of cycles configured are completed\.

1. 

**Using CMake with FreeRTOS**  
You can also use CMake to build and run the demo application\. To set up CMake and a native build system, see [Prerequisites](building-cmake-prereqs.md)\.

   1. Use the following command to generate build files\. Specify the target board with the `-DBOARD` option\. 

      ```
      cmake -DVENDOR=cypress -DBOARD=CY8CKIT_064S0S2_4343W -DCOMPILER=arm-gcc -S freertos -B build_dir
      ```

      If you're using Windows, you must specify the native build system using the `-G` option because CMake uses Visual Studio by default\.   
**Example**  

      ```
      cmake -DVENDOR=cypress -DBOARD=CY8CKIT_064s0s2_4343W -DCOMPILER=arm-gcc -S freertos -B build_dir -G Ninja
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

1. 

**Monitoring MQTT Messages on the Cloud**  
You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\. To subscribe to the MQTT topic with the AWS IoT MQTT client, follow these steps\.

   1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

   1. In the navigation pane, choose **Test** to open the MQTT client\.

   1. In the **Subscription topic** textbox enter `iotdemo/#`\. For **Quality of Service** select **1 \- This client will acknowledge to the Device Gateway that messages are received**\. For **MQTT payload display** select **Display payloads as strings \(more accurate\)**\. Then choose **Subscribe to topic**\.

      A hash \(\#\) symbol at the end of a topic name acts as a wildcard\. For example, if you subscribe to the topic `iotdemo/#` you receive messages published to any topic that starts with **iotdemo/**\. You can change the topic name by modifying the macro `IOT_DEMO_MQTT_TOPIC_PREFIX` in the `freertos/demos/mqtt/iot_demo_mqtt.c` file\.

   1. Reset the kit to force it to publish MQTT messages, which can then be seen on the console test client\.

### Running other demos<a name="getting_started_cypress_psoc64_demos"></a>

The following demo applications have been tested and verified to work with the current release\. You can find these demos under the `freertos/demos` directory\. For information on how to run these demos, see [FreeRTOS Demos](freertos-next-steps.md)\.
+ Bluetooth Low Energy demo
+ Over\-the\-Air Updates demo
+ Secure Sockets Echo Client demo
+ AWS IoT Device Shadow demo

## Debugging<a name="getting_started_cypress_psoc64_debugging"></a>

The KitProg on the kit supports debugging over the SWD protocol\.
+ To debug the FreeRTOS application, select the **aws\_demos project** in the workspace and then select **aws\_demos Debug \(KitProg3\)** from the **Quick Panel**\.

## OTA updates<a name="getting_started_cypress_psoc64_ota_updates"></a>

PSoC 64 devices have passed all of the required FreeRTOS qualification tests\. However, the optional over\-the\-air \(OTA\) feature implemented in the PSoC 64 Standard Secure AWS firmware library is still pending evaluation\. The OTA feature as\-implemented currently passes all of the OTA qualification tests except [ aws\_ota\_test\_case\_rollback\_if\_unable\_to\_connect\_after\_update\.py](https://github.com/aws/amazon-freertos/tree/202007.00/tools/ota_e2e_tests/aws_ota_test/aws_ota_test_case_rollback_if_unable_to_connect_after_update.py)\. 

When a successfully validated OTA image is applied to a device using the PSoC64 Standard Secure AWS MCU and the device can't communicate with AWS IoT Core, the device can't automatically rollback to the original known good image\. This might result in the device being unreachable from AWS IoT Core for further updates\. This functionality is still under development by the Cypress team\.

For more information, see [OTA Updates with AWS and the CY8CKIT\-064S0S2\-4343W Kit](https://community.cypress.com/docs/DOC-20063)\. If you have further questions or need technical support, contact the [Cypress Developer Community](https://community.cypress.com/community/software-forums/modustoolbox-amazon-freertos-sdk)\.
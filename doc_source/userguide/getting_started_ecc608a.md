# Getting Started with the Microchip ECC608a Secure Element with Windows Simulator<a name="getting_started_ecc608a"></a>

This tutorial provides instructions for getting started with the Microchip ECC608a Secure Element with Windows Simulator\.

You need the following hardware:
+ [Microchip ECC608a secure element clickboard](https://www.mikroe.com/secure-4-click)
+ [ SAMD21 XPlained Pro](https://www.microchipdirect.com/product/ATSAMD21-XPRO?dfw_tracker=64197-ATSAMD21-XPRO&gclid=EAIaIQobChMIn5jIuM3C5QIVk_5kCh1m1Ag4EAQYASABEgLKtfD_BwE)
+ [ mikroBUS Xplained Pro adapter](https://www.microchip.com/Developmenttools/ProductDetails/ATMBUSADAPTER-XPRO)

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\.  In this tutorial, the path to the FreeRTOS download directory is referred to as *freertos*\.

## Overview<a name="gsg-ecc608a-overview"></a>

This tutorial contains the following steps:

1. Connect your board to a host machine\.

1. Install software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross\-compile an FreeRTOS demo application to a binary image\.

1. Load the application binary image to your board, and then run the application\.

## Set Up the Microchip ECC608a Hardware<a name="gsg-ecc608a-setup"></a>

Before you can interact with your Microchip ECC608a device, you must first program the SAMD21\.

**To set up the SAMD21 XPlained Pro board**

1. Follow the [CryptoAuthSSH\-XSTK \(DM320109\) \- Latest Firmware](http://ww1.microchip.com/downloads/en/DeviceDoc/ATCRYPTOAUTHSSH-XSTK_v1.0.1.zip) link to download a \.zip file containing instructions \(PDF\) and a binary which can be programmed onto the D21\.

1. Download and install the [Amtel Studio 7](https://www.microchip.com/mplab/avr-support/atmel-studio-7) IDP\. Make sure that you select the **SMART ARM MCU** driver architecture during installation\.

1. Use a USB 2\.0 Micro B cable to attach the "Debug USB" connector to your computer, and follow the instructions in the PDF\. \(The "Debug USB" connector is the USB port closest to the POWER led and pins\.\)

**To connect the hardware**

1. Unplug the micro USB cable from Debug USB\.

1. Plug the mikroBUS XPlained Pro adapter into the SAMD21 board in the EXT1 location\.

1. Plug the ATECC608a Secure 4 Click board into the mikroBUSX XPlained Pro adapter\. Make sure that the notched corner of the click board matches with the notched icon on the adapter board\.

1. Plug the micro USB cable into Target USB\.

Your setup should look like the following\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/samd21.png)

## Set Up Your Development Environment<a name="gsg-ecc608a-setup-dev-env"></a>

1. If you haven't already, [create an AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\. To add an IAM user to your AWS account, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

   To grant your IAM user account access to AWS IoT and FreeRTOS, you attach the following IAM policies to your IAM user account in these steps:
   + `AmazonFreeRTOSFullAccess`
   + `AWSIoTFullAccess`

1. Attach the `AmazonFreeRTOSFullAccess` policy to your IAM user\.

   1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

   1. Enter your user name in the search text box, and then choose it from the list\.

   1. Choose **Add permissions**\.

   1. Choose **Attach existing policies directly**\.

   1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

   1. Choose **Add permissions**\.

1. Attach the `AWSIoTFullAccess` policy to your IAM user\.

   1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

   1. Enter your user name in the search text box, and then choose it from the list\.

   1. Choose **Add permissions**\.

   1. Choose **Attach existing policies directly**\.

   1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

   1. Choose **Add permissions**\.

   For more information about IAM, see [IAM Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html) in the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

1. Download the FreeRTOS repo from  the [FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

   To download FreeRTOS from GitHub:

   1. Browse to the [FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

   1. Choose **Clone or download**\.

   1. From the command line on your computer, clone the repository to a directory on your host machine\.

      ```
      git clone https://github.com/aws/amazon-freertos.git --recurse-submodules
      ```
**Important**  
In this topic, the path to the FreeRTOS download directory is referred to as `freertos`\.
Space characters in the `freertos` path can cause build failures\. When you clone or copy the repository, make sure the path that you create doesn't contain space characters\.
The maximum length of a file path on Microsoft Windows is 260 characters\. Long FreeRTOS download directory paths can cause build failures\.

   1. From the `freertos` directory, check out the branch to use\.

1. Set up your development environment\.

   1. Install the latest version of [WinPCap](https://www.winpcap.org)\.

   1. Install Microsoft Visual Studio\.

      Visual Studio versions 2017 and 2019 are known to work\. All editions of these Visual Studio versions are supported \(Community, Professional, or Enterprise\)\.

      In addition to the IDE, install the Desktop development with C\+\+ component\. Then, under **Optional**, install the latest Windows 10 SDK\. 

   1. Make sure that you have an active hard\-wired Ethernet connection\.

## Build and Run the FreeRTOS Demo Project<a name="gsg-ecc608a-build-and-run"></a>

**Important**  
The Microchip ECC608a device has a one time initialization that is locked onto the device the first time a project is run \(during the call to `C_InitToken`\)\. However, the FreeRTOS demo project and test project have different configurations\. If the device is locked during the demo project configurations, it will not be possible for all tests in the test project to succeed\.

**To build and Run the FreeRTOS Demo Project with the Visual Studio IDE**

1. Load the project into Visual Studio\.

   From the **File** menu, choose **Open**\. Choose **File/Solution**, navigate to the `freertosprojects\microchip\ecc608a_plus_winsim\visual_studio\aws_demos\aws_demos.sln` file, and then choose **Open**\.

1. Retarget the demo project\.

   The demo project depends on the Windows SDK, but it does not have a Windows SDK version specified\. By default, the IDE might attempt to build the demo with an SDK version not present on your machine\. To set the Windows SDK version, right\-click **aws\_demos**, and then choose **Retarget Projects**\. This opens the **Review Solution Actions** window\. Choose a Windows SDK version that is present on your machine \(use the initial value in the drop\-down list\), and then choose **OK**\.

1. Build and run the project\.

   From the **Build** menu, choose **Build Solution**, and make sure the solution builds without errors\. Choose **Debug, Start Debugging** to run the project\. On the first run, you need to configure your device interface and recompile\. For more information, see [Configure Your Network Interface](getting_started_windows.md#win-network-interface)\.

1. Provision the Microchip ECC608a\.

   Microchip has provided several scripting tools to help with the setup of the ATECC608a parts\. Navigate to `freertos\vendors\microchip\secure_elements\app\example_trust_chain_tool`, and open the README\.md file\.

   Follow the instructions in the `README.md` file to provision your device\. The steps include the following:

   1. Create and register a certificate authority with AWS\.

   1. Generate your keys on the Microchip ECC608a and export the public key and device serial number\.

   1. Generate a certificate for the device and registering that certificate with AWS\.

   1. Load the CA certificate and device certificate onto the device\.

1. Build and run FreeRTOS samples\. 

   Re\-run the demo project again\. This time you should connect\!

## Troubleshooting<a name="ecc680a-troubleshooting"></a>

For general troubleshooting information, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
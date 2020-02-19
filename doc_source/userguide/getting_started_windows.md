# Getting Started with the Windows Device Simulator<a name="getting_started_windows"></a>

This tutorial provides instructions for getting started with the FreeRTOS Windows Device Simulator\.

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the FreeRTOS download directory is referred to as `freertos`\.

FreeRTOS is released as a zip file that contains the FreeRTOS libraries and sample applications for the platform you specify\. To run the samples on a Windows machine, download the libraries and samples ported to run on Windows\. This set of files is referred to as the FreeRTOS simulator for Windows\.

## Set Up Your Development Environment<a name="win-setup-env"></a>

1. Install the latest version of [WinPCap](https://www.winpcap.org/)\.

1. Install [Microsoft Visual Studio](https://www.visualstudio.com/downloads)\.

   Visual Studio versions 2017 and 2019 are known to work\. All editions of these Visual Studio versions are supported \(Community, Professional, or Enterprise\)\.

   In addition to the IDE, install the **Desktop development with C\+\+** component\.

   Install the latest Windows 10 SDK\. You can choose this under the **Optional** section of the **Desktop development with C\+\+** component\.

1. Make sure that you have an active hard\-wired Ethernet connection\.

1. \(Optional\) If you would like to use the CMake\-based build system to build your FreeRTOS projects, install the latest version of [CMake](https://cmake.org/download/)\. FreeRTOS requires CMake version 3\.13 or later\.

## Build and Run the FreeRTOS Demo Project<a name="win-build-and-run-example"></a>

You can use Visual Studio or CMake to build FreeRTOS projects\.

### Building and Running the FreeRTOS Demo Project with the Visual Studio IDE<a name="win-build-and-run-vs"></a>

1. Load the project into Visual Studio\.

   In Visual Studio, from the **File** menu, choose **Open**\. Choose **File/Solution**, navigate to the `projects/pc/windows/visual_studio/aws_demos/aws_demos.sln` file, and then choose **Open**\.

1. Retarget the Demo Project\.

   The provided demo project depends on the Windows SDK, but it does not have a Windows SDK version specified\. By default, the IDE might attempt to build the demo with an SDK version not present on your machine\. To set the Windows SDK version, right\-click on `aws_demos` and then choose **Retarget Projects**\. This opens the **Review Solution Actions** window\. Choose a Windows SDK Version that is present on your machine \(the initial value in the dropdown is fine\), then choose **OK**\.

1. Build and run the project\.

   From the **Build** menu, choose **Build Solution**, and make sure the solution builds without errors or warnings\. Choose **Debug**, **Start Debugging** to run the project\. On the first run, you will need to [select a network interface](#win-network-interface)\.

### Building and Running the FreeRTOS Demo Project with CMake<a name="win-build-and-run-cmake"></a>

We recommend that you use the CMake GUI instead of the CMake command\-line tool to build the demo project for the Windows Simulator\.

After you install CMake, open the CMake GUI\. On Windows, you can find this from the Start menu under **CMake**, **CMake \(cmake\-gui\)**\.

1. Set the FreeRTOS source code directory\.

   In the GUI, set the FreeRTOS source code directory \(`freertos`\) for **Where is the source code**\.

   Set `freertos/build` for **Where to build the binaries**\.

1. Configure the CMake Project\.

   In the CMake GUI, choose **Add Entry**, and on the **Add Cache Entry** window, set the following values:  
Name  
AFR\_BOARD  
Type  
STRING  
Value  
pc\.windows  
Description  
\(Optional\)

1. Choose **Configure**\. If CMake prompts you to create the build directory, choose **Yes**, and then select a generator under **Specify the generator for this project**\. We recommend using Visual Studio as the generator, but Ninja is also supported\. \(Note that when using Visual Studio 2019, the platform should be set to Win32 instead of its default setting\.\) Leave the other generator options unchanged and choose **Finish**\.

1. Generate and Open the CMake Project\.

   After you have configured the project, the CMake GUI shows all options available for the generated project\. For the purposes of this tutorial, you can leave the options as their default values\.

   Choose **Generate** to create a Visual Studio solution, and then choose **Open Project** to open the project in Visual Studio\.

   In Visual Studio, right\-click the `aws_demos` project and choose **Set as StartUp Project**\. This enables you to build and run the project\. On the first run, you will need to [select a network interface](#win-network-interface)\.

For more information about using CMake with FreeRTOS, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

### Configure Your Network Interface<a name="win-network-interface"></a>

On the first run of the demo project, you must select the network interface to use\. The program enumerates your network interfaces\. Find the number for your hard\-wired Ethernet interface\. The output should look like this:

```
0 0 [None] FreeRTOS_IPInit
1 0 [None] vTaskStartScheduler
1. rpcap://\Device\NPF_{AD01B877-A0C1-4F33-8256-EE1F4480B70D}
(Network adapter 'Intel(R) Ethernet Connection (4) I219-LM' on local host)

2. rpcap://\Device\NPF_{337F7AF9-2520-4667-8EFF-2B575A98B580}
(Network adapter 'Microsoft' on local host)

The interface that will be opened is set by "configNETWORK_INTERFACE_TO_USE", which
should be defined in FreeRTOSConfig.h

ERROR:  configNETWORK_INTERFACE_TO_USE is set to 0, which is an invalid value.
Please set configNETWORK_INTERFACE_TO_USE to one of the interface numbers listed above,
then re-compile and re-start the application.  Only Ethernet (as opposed to Wi-Fi)
interfaces are supported.
```

After you have identified the number for your hard\-wired Ethernet interface, close the application window\. In the example above, the number to use is `1`\.

Open `FreeRTOSConfig.h` and set `configNETWORK_INTERFACE_TO_USE` to the number that corresponds to your hard\-wired network interface\.

**Important**  
Only Ethernet interfaces are supported\. Wi\-Fi isn't supported\. For more information, see the *WinPcap FAQ* entry [Q\-16: Which network adapters are supported by WinPcap? ](https://www.winpcap.org/misc/faq.htm#Q-16)\.

### Monitoring MQTT Messages on the Cloud<a name="getting_started_windows_monitor_mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="windows-troubleshooting"></a>

### Troubleshooting Common Problems on Windows<a name="windows-troubleshooting-common"></a>

You might run into the following error when trying to build the demo project with Visual Studio:

```
Error "The Windows SDK version X.Y was not found" when building the provided Visual Studio solution.
```

The project must be targeted to a Windows SDK version present on your computer\.

For general troubleshooting information about Getting Started with FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
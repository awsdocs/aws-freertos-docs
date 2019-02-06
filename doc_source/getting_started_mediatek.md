# Getting Started with the MediaTek MT7697Hx Development Kit<a name="getting_started_mediatek"></a>

Before you begin, see [First Steps](freertos-prereqs.md)\.

If you do not have the MediaTek MT7697Hx Development Kit, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AAOmPUAX/MT7697Hx-Development-Kit)\.

## Setting Up Your Environment<a name="mediatek-setup-env"></a>

Before you set up your environment, connect your computer to the USB port on the MediaTek MT7697Hx Development Kit\.

### Download and Install Keil MDK<a name="install-keil-mdk"></a>

You can use the GUI\-based Keil Microcontroller Development Kit \(MDK\) to configure, build, and run Amazon FreeRTOS projects on your board\. Keil MDK includes the μVision IDE and the μVision Debugger\.

**Note**  
Keil MDK is supported on Windows 7, Windows 8, and Windows 10 64\-bit machines only\.

**To download and install Keil MDK**

1. Go to the [Keil MDK Getting Started](http://www2.keil.com/mdk5/install/) page, and choose **Download MDK\-Core**\.

1. Enter and submit your information to register with Keil\.

1. Right\-click the MDK executable and save the Keil MDK installer to your computer\.

1. Open the Keil MDK installer and follow the steps to completion\. Make sure that you install the MediaTek device pack \(MT76x7 Series\)\.

### Set Up a Serial Connection<a name="mediatek-serial-connection"></a>

To establish a serial connection with the MediaTek MT7697Hx Development Kit, you must install the Arm Mbed Windows serial port driver\. You can download the driver from [Mbed](https://os.mbed.com/docs/latest/tutorials/windows-serial-driver.html)\. Follow the steps on the **Windows serial driver** page to download and install the driver for the MediaTek MT7697Hx Development Kit\. 

After you install the driver, a COM port appears in the Windows Device Manager\. For debugging, you can open a session to the port with a terminal utility tool such as HyperTerminal or TeraTerm\.

## Build and Run the Amazon FreeRTOS Demo Project with Keil MDK<a name="mediatek-build-and-run-keil"></a>

**To build the Amazon FreeRTOS demo project in Keil μVision**

1. From the **Start** menu, open Keil μVision 5\.

1. Open the `<BASE_FOLDER>/demos/mediatek/mt7697hx-dev-kit/uvision/aws_demo.uvprojx` project file\.

1. From the menu, choose **Project**, and then choose **Build target**\.

   After the code is built, you see the demo executable file at `<BASE_FOLDER>/demos/mediatek/mt7697hx-dev-kit/uvision/out/Objects/aws_demo.axf`\.

**To run the Amazon FreeRTOS demo project**

1. Set the MediaTek MT7697Hx Development Kit to PROGRAM mode\.

   To set the kit to PROGRAM mode, press and hold the **PROG** button\. With the **PROG** button still pressed, press and release the **RESET** button, and then release the **PROG** button\.

1. From the menu, choose **Flash**, and then choose **Configure Flash Tools**\.

1. In **Options for Target '`aws_demo`'**, choose the **Debug** tab\. Select **Use**, set the debugger to **CMSIS\-DAP Debugger**, and then choose **OK**\.

1. From the menu, choose **Flash**, and then choose **Download**\.

   μVision notifies you when the download is complete\.

1. Use a terminal utility to open the serial console window\. Set the serial port to 115200 bps, none\-parity, 8\-bits, and 1 stop\-bit\.

1. Choose the **RESET** button on your MediaTek MT7697Hx Development Kit\.

## Troubleshooting<a name="mediatek-troubleshooting"></a>

### Debugging Amazon FreeRTOS Projects in Keil μVision<a name="mediatek-debugging"></a>

Currently, you must edit the MediaTek package that is included with Keil μVision before you can debug the Amazon FreeRTOS demo project for MediaTek with Keil μVision\.

**To edit the MediaTek package for debugging Amazon FreeRTOS projects**

1. Find and open the `Keil_v5\ARM\PACK\MediaTek\MTx\4.6.1\MediaTek.MTx.pdsc` file in your Keil MDK installation folder\.

1. Replace all instances of `flag = Read32(0x20000000);` with `flag = Read32(0x0010FBFC);`\.

1. Replace all instances of `Write32(0x20000000, 0x76877697);` with `Write32(0x0010FBFC, 0x76877697);`\.

**To start debugging the project**

1. From the menu, choose **Flash**, and then choose **Configure Flash Tools**\.

1. Choose the **Target** tab, and then choose **Read/Write Memory Areas**\. Confirm that IRAM1 and IRAM2 are both selected\.

1. Choose the **Debug** tab, and then choose **CMSIS\-DAP Debugger**\.

1. Open `<BASE_FOLDER>/demos/mediatek/mt7697hx-dev-kit/common/application_code/main.c`, and set the macro `MTK_DEBUGGER` to `1`\. 

1. Rebuild the demo project in μVision\. 

1. Set the MediaTek MT7697Hx Development Kit to PROGRAM mode\.

   To set the kit to PROGRAM mode, press and hold the **PROG** button\. With the **PROG** button still pressed, press and release the **RESET** button, and then release the **PROG** button\.

1. From the μVision menu, choose **Debug**, and then choose **Start/Stop Debug Session**\. The **Call Stack \+ Locals** window opens when you start the debug session\. μVision flashes the demo to the board, runs the demo, and stops at the beginning of the `main()` function\. 

1. From the menu, choose **Debug**, and then choose **Stop** to stop the session\. The program counter stops at the following line: 

   ```
   { volatile int wait_ice = 1 ; while ( wait_ice ) ; }
   ```

1. In the **Call Stack \+ Locals** window, change the value for `wait_ice` to `0`\. 

1. Set breakpoints in your project's source code, and run the code\. 

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
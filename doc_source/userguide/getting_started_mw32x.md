# Getting started with the MW32x AWS IoT Starter Kit<a name="getting_started_mw32x"></a>

The AWS IoT Starter Kit is a development kit based on the 88MW320/88MW322, the latest integrated Cortex M4 Microcontroller from NXP, that integrates 802\.11b/g/n Wi\-Fi on a single microcontroller chip\. The development kit is FCC certified\. For more information, see the [AWS Partner Device Catalog](https://devices.amazonaws.com/detail/a3G0h000000OaRnEAK/MW320-AWS-IoT-Starter-Kit) to purchase one from our partner\. The 88MW320/88MW322 modules are also FCC certified and available for customization and volume sale\.

This getting started guide shows you how to cross compile your application together with the SDK on a host computer, then load the generated binary file onto the board using the tools provided with the SDK\. When the application starts running on the board, you can debug or interact with it from the Serial console on your host computer\.

Ubuntu 16\.04 is the host platform supported for development and debugging\. You might be able to use other platforms, but these are not officially supported\. You must have permissions to install software on the host platform\. The following external tools required to build the SDK:
+ Ubuntu 16\.04 host platform
+ ARM toolchain version 4\_9\_2015q3
+ Eclipse 4\.9\.0 IDE

The ARM toolchain is required to cross compile your application and the SDK\. The SDK takes advantage of the latest versions of the toolchain to optimize the image footprint and fit more functionality into less space\. This guide assumes that you're using version 4\_9\_2015q3 of the toolchain\. Using older versions of the toolchain isn't recommended\. The development kit is pre\-flashed with Wireless Microcontroller Demo project firmware\.

**Topics**
+ [Setting up your hardware](#gsg-mw32x-hardware)
+ [Setting up the development environment](#gsg-mw32x-dev-env)
+ [Build and run the FreeRTOS demo project](#gsg-mw32x-build-and-run)
+ [Debugging](#gsg-mw32x-debugging)
+ [Troubleshooting](#gsg-mw32x-troubleshooting)

## Setting up your hardware<a name="gsg-mw32x-hardware"></a>

Connect the MW32x board to your laptop by using a mini\-USB to USB cable\. Connect the mini\-USB cable to the only mini\-USB connector present on the board\. You don't need a jumper change\. 

If the board is connected to a laptop or desktop computer, you don't need an external power supply\.

This USB connection provides the following:
+ Console access to the board\. A virtual tty/com port is registered with the development host that can be used to access the console\.
+ JTAG access to the board\. This can be used to load or unload firmware images into the RAM or flash of the board, or for debugging purposes\.

## Setting up the development environment<a name="gsg-mw32x-dev-env"></a>

For development purposes, the minimum requirement is the ARM toolchain and the tools bundled with the SDK\. The following sections provide details on the ARM toolchain setup\. 

### GNU Toolchain<a name="gsg-mw32x-dev-env-gnu"></a>

The SDK officially supports the GCC Compiler toolchain\. The cross\-compiler toolchain for GNU ARM is available at [GNU Arm Embedded Toolchain 4\.9\-2015\-q3\-update](https://launchpad.net/gcc-arm-embedded/4.9/4.9-2015-q3-update)\.

The build system is configured to use the GNU toolchain by default\. The Makefiles assume that the GNU compiler toolchain binaries are available on the user's PATH and can be invoked from the Makefiles\. The Makefiles also assume that the file names of the GNU toolchain binaries are prefixed with `arm-none-eabi-`\.

The GCC toolchain can be used with GDB to debug with OpenOCD \(bundled with the SDK\)\. This provides the software that interfaces with JTAG\.

We recommend version 4\_9\_2015q3 of the gcc\-arm\-embedded toolchain\.

### Linux Toolchain Setup Procedure<a name="gsg-mw32x-dev-env-linux"></a>

Follow these steps to set up the GCC toolchain in Linux\.

1. Download the toolchain tarball available at [GNU Arm Embedded Toolchain 4\.9\-2015\-q3\-update](https://launchpad.net/gcc-arm-embedded/4.9/4.9-2015-q3-update)\. The file is `gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar.bz2`\.

1. Copy the file to a directory of your choice\. Make sure there are no spaces in the directory name\.

1. Use the following command to untar the file\.

   ```
   tar –vxf filename
   ```

1. Add the path of the installed toolchain to the system PATH\. For example, append the following line at the end of the `.profile` file located in `/home/user-name` directory\.

   ```
   PATH=$PATH:path to gcc-arm-none-eabit-4_9_2015_q3/bin
   ```

**Note**  
Newer distributions of Ubuntu might come with a Debian version of the GCC Cross Compiler\. If so, you must remove the native Cross Compiler and follow the above setup procedure\.

### Working with a Linux development host<a name="gsg-mw32x-dev-env-linux-host"></a>

Any modern Linux desktop distribution such as Ubuntu or Fedora can be used\. However, we recommend that you upgrade to the most recent release\. The following steps have been verified to work on Ubuntu 16\.04 and assume that you're using that version\.

#### Installing Packages<a name="gsg-mw32x-dev-env-linux-host-pkgs"></a>

The SDK includes a script to enable quick setup of your development environment on a newly setup Linux machine\. The script attempts to auto detect the machine type and install the appropriate software, including C libraries, USB library, FTDI library, ncurses, python, and latex\. In this section, the generic directory name `amzsdk_bundle-x.y.z` indicates the AWS SDK root directory\. The actual directory name might be different\. You must have root privileges\.
+ Navigate to the `amzsdk_bundle-x.y.z/` directory and run this command\.

  ```
  ./lib/third_party/mcu_vendor/marvell/WMSDK/mw320/sdk/tools/bin/installpkgs.sh
  ```

#### Avoiding sudo<a name="gsg-mw32x-dev-env-linux-host-sudo"></a>

In this guide, the `flashprog` operation uses the `flashprog.py` script to flash the NAND of the board, as explained below\. Similarly, the `ramload` operation uses the `ramload.py` script to copy the firmware image from the host directly to the RAM of the microcontroller, without flashing the NAND\.

You can configure your Linux development host to perform the `flashprog` and `ramload` operations without requiring the `sudo` command each time\. To do this, run the following command\.

```
./lib/third_party/mcu_vendor/marvell/WMSDK/mw320/sdk/tools/bin/perm_fix.sh
```

**Note**  
You must configure your Linux development host permissions in this way to ensure a smooth Eclipse IDE experience\.

#### Setting up the Serial Console<a name="gsg-mw32x-dev-env-linux-host-serial"></a>

Insert the USB cable into the Linux host USB slot\. This triggers the detection of the device\. You should see messages like the following in the `/var/log/messages` file, or after executing the `dmesg` command\.

```
Jan 6 20:00:51 localhost kernel: usb 4-2: new full speed USB device using uhci_hcd and address 127
Jan 6 20:00:51 localhost kernel: usb 4-2: configuration #1 chosen from 1 choice
Jan 6 20:00:51 localhost kernel: ftdi_sio 4-2:1.0: FTDI USB Serial Device converter detected
Jan 6 20:00:51 localhost kernel: ftdi_sio: Detected FT2232C
Jan 6 20:00:51 localhost kernel: usb 4-2: FTDI USB Serial Device converter now attached to ttyUSB0
Jan 6 20:00:51 localhost kernel: ftdi_sio 4-2:1.1: FTDI USB Serial Device converter detected
Jan 6 20:00:51 localhost kernel: ftdi_sio: Detected FT2232C
Jan 6 20:00:51 localhost kernel: usb 4-2: FTDI USB Serial Device converter now attached to ttyUSB1
```

Verify that two ttyUSB devices have been created\. The second ttyUSB is the serial console\. In the example above, this is named "ttyUSB1"\. 

In this guide, we use minicom to see the serial console output\. You might also use other serial programs such as `putty`\. Run the following command to execute minicom in setup mode\.

```
minicom –s
```

In minicom, navigate to **Serial Port Setup** and capture the following settings\.

```
| A - Serial Device : /dev/ttyUSB1
| B – Lockfile Location : /var/lock
| C - Callin Program :
| D - Callout Program :
| E - Bps/Par/Bits : 115200 8N1
| F – Hardware Flow Control : No
| G – Software Flow Control : No
```

You can save these settings in minicom for future use\. The minicom window now shows messages from the serial console\.

Choose the serial console window and press the **Enter** key\. This displays a hash \(\#\) on the screen\. 

**Note**  
The development boards include an FTDI silicon device\. The FTDI device exposes two USB interfaces for the host\. The first interface is associated with the JTAG functionality of the MCU and the second interface is associated with the physical UARTx port of the MCU\.

#### Installing OpenOCD<a name="gsg-mw32x-dev-env-linux-host-openocd"></a>

OpenOCD is software that provides debugging, in\-system programming, and boundary\-scan testing for embedded target devices\.

OpenOCD version 0\.9 is required\. It's also required for Eclipse functionality\. If an earlier version, such as version 0\.7, was installed on your Linux host, remove that repository with the appropriate command for the Linux distribution that you're currently using\.

Run the standard Linux command to install OpenOCD,

```
apt-get install openocd
```

If the above command doesn't install version 0\.9 or later, use the following procedure to download and compile the openocd source code\.

**To install OpenOCD**

1. Run the following command to install libusb\-1\.0\.

   ```
   sudo apt-get install libusb-1.0 
   ```

1. Download the openocd 0\.9\.0 source code from [http://openocd\.org/](http://openocd.org/)\.

1. Extract openocd and navigate to the directory where you extracted it\.

1. Configure openocd with the following command\.

   ```
   ./configure --enable-ftdi --enable-jlink
   ```

1. Run the make utility to compile opencd\.

   ```
   make install
   ```

### Setting up Eclipse<a name="gsg-mw32x-eclipse"></a>

**Note**  
This section assumes that you have completed the steps in [Avoiding sudo](#gsg-mw32x-dev-env-linux-host-sudo)

Eclipse is the preferred IDE for application development and debugging\. It provides a rich, user\-friendly IDE with integrated debugging support, including thread aware debugging\. This section describes the common Eclipse setup for all the development hosts that are supported\.

**To set up Eclipse**

1. Download and install the Java Run Time Environment \(JRE\)\.

   Eclipse requires that you install the JRE\. We recommend that you install this first, although it can be installed after you install Eclipse\. The JRE version \(32/64 bit\) must match the version of Eclipse \(32/64 bit\)\. You can download the JRE from [Java SE Runtime Environment 8 Downloads](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) on the Oracle website\.

1. Download and install the "Eclipse IDE for C/C\+\+ Developers" from [http://www\.eclipse\.org](http://www.eclipse.org)\. Eclipse version 4\.9\.0 or later is supported\. The installation only requires you to extract the downloaded archive\. You run the platform\-specific Eclipse executable to start the application\. 

## Build and run the FreeRTOS demo project<a name="gsg-mw32x-build-and-run"></a>

There are two ways to run the FreeRTOS demo project:
+ Use the command line\.
+ Use the Eclipse IDE\.

This topic covers both options\.

**Provisioning**
+ Depending on whether you use the test or demo application, set the provisioning data in one of the following files:
  + `./tests/common/include/aws_clientcredential.h` 
  + `./demos/common/include/aws_clientcredential.h` 

  For example:

  ```
  #define clientcredentialWIFI_SSID "Wi-Fi SSID"
  #define clientcredentialWIFI_PASSWORD "Wi-Fi password"
  #define clientcredentialWIFI_SECURITY "Wi-Fi security"
  ```
**Note**  
You can enter the following Wi\-Fi security values:   
`eWiFiSecurityOpen`
`eWiFiSecurityWEP`
`eWiFiSecurityWPA`
`eWiFiSecurityWPA2`
The SSID and password should be enclosed in double quotes\.

**Build and run the FreeRTOS demo using the command line**

1. Use the following command to start building the demo application\.

   ```
   cmake -DVENDOR=marvell -DBOARD=mw320 -DCOMPILER=arm-gcc -S .  -Bbuild -DAFR_ENABLE_TESTS=0
   ```

   Make sure you get the same output as shown in the following example\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-build-demo-app1.png)

1. Navigate to the build directory\.

   ```
   cd build
   ```

1. Run the make utility to build the application\.

   ```
   make all -j4
   ```

   Make sure you get the same output as shown in the following figure:   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-build-demo-app2.png)

1. Use the following commands to build a test application\.

   ```
   cmake -DVENDOR=marvell -DBOARD=mw320 -DCOMPILER=arm-gcc -S .  -Bbuild -DAFR_ENABLE_TESTS=1
   cd build
   make all -j4
   ```

   Run the `cmake` command every time you switch between the `aws_demos project` and the `aws_tests project`\.

1. Write the firmware image to the flash of the development board\. The firmware will execute after the development board is reset\. You must build the SDK before you flash the image to the microcontroller\.

   1. Before you flash the firmware image, prepare the development board's flash with the common components Layout and Boot2\. Use the following commands\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py -l ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/mw300/layout.txt --boot2 ./vendors/marvell/WMSDK/mw320/boot2/bin/boot2.bin
      ```

      The `flashprog` command initiates the following:
      + Layout – The flashprog utility is first instructed to write a layout to the flash\. The layout is similar to partition information for the flash\. The default layout is located at `/lib/third_party/mcu_vendor/marvell/WMSDK/mw320/sdk/tools/OpenOCD/mw300/layout.txt`\. 
      + Boot2 – This is the boot\-loader used by the WMSDK\. The `flashprog` command also writes a bootloader to the flash\. It's the bootloader's job to load the microcontroller's firmware image after it's flashed\. Make sure you get the same output as shown in the figure below\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-output-flash-layout.png)

   1. The firmware uses the Wi\-Fi chipset for its functionality, and the Wi\-Fi chipset has its own firmware that must also be present in the flash\. You use the `flashprog.py` utility to flash the Wi\-Fi firmware in the same way that you did to flash the Boot2 boot\-loader and the MCU firmware\. Use the following commands to flash the Wi\-Fi firmware\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --wififw ./vendors/marvell/WMSDK/mw320/wifi-firmware/mw30x/mw30x_uapsta_W14.88.36.p135.bin
      ```

      Make sure the output of the command is similar to the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-flash-wifi-firmware.png)

   1.  Use the following commands to flash the MCU firmware\.

      ```
      cd amzsdk_bundle-x.y.z 
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --mcufw build/cmake/vendors/marvell/mw300_rd/aws_demos.bin -r
      ```

   1. Reset the board\. You should see the logs for the demo app\.

   1. To run the test app, flash the `aws_tests.bin` binary located at the same directory\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --mcufw build/cmake/vendors/marvell/mw300_rd/aws_tests.bin -r
      ```

      Your command output should be similar to the one shown in the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-flash-mcu-firmware.png)

1. After you flash the firmware and reset the board, the demo app should start as shown in the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-demo-app-start.png)

1. \(Optional\) As an alternative method to test your image, use the flashprog utility to copy the microcontroller image from the host directly into the microcontroller RAM\. The image isn't copied in the flash, so it will be lost after you reboot the microcontroller\.

   Loading the firmware image into the SRAM is a faster operation because it launches the execution file immediately\. This method is used primarily for iterative development\.

   Use the following commands to load the firmware into the SRAM\.

   ```
   cd amzsdk_bundle-x.y.z
   ./lib/third_party/mcu_vendor/marvell/WMSDK/mw320/sdk/tools/OpenOCD/ramload.py build/cmake/vendors/marvell/mw300_rd/aws_demos.axf
   ```

   The command output is shown in the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-load-firmware-sram.png)

   When the command execution is complete, you should see the logs of the demo app\.

**Build and run the FreeRTOS demo using the Eclipse IDE**

1. Before you set up an Eclipse workspace, you must run the `cmake` command\.

   Run the following command to work with the `aws_demos` Eclipse project\.

   ```
   cmake -DVENDOR=marvell -DBOARD=mw320 -DCOMPILER=arm-gcc -S .  -Bbuild -DAFR_ENABLE_TESTS=0
   ```

   Run the following command to work with the `aws_tests` Eclipse project\.

   ```
   cmake -DVENDOR=marvell -DBOARD=mw320 -DCOMPILER=arm-gcc -S .  -Bbuild -DAFR_ENABLE_TESTS=1
   ```
**Tip**  
Run the `cmake` command every time you switch between the `aws_demos` project and the `aws_tests` project\.

1. Open Eclipse and, when prompted, choose your Eclipse workspace as shown in the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-select-eclipse-workspace.png)

1. Choose the option to create a **Makefile Project: with Existing Code** as shown in the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-create-makefile-project.png)

1. Choose **Browse**, specify the directory of the existing code, and then choose **Finish**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-locate-existing-code.png)

1. In the navigation pane, choose **aws\_demos** in the project explorer\. Right\-click **aws\_demos** to open the menu, then choose **Build**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-building-aws_demos.png)

   If the build succeeds, it generates the `build/cmake/vendors/marvell/mw300_rd/aws_demos.bin` file\.

1. Use the command line tools to flash the Layout file \(`layout.txt`\), the Boot2 binary \(`boot2.bin`\), the MCU firmware binary \(`aws_demos.bin`\), and the Wi\-Fi firmware\.

   1. Before you flash the firmware image, prepare the development board's flash with the common components, Layout and Boot2\. Use the following commands\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py -l ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/mw300/layout.txt --boot2 ./vendors/marvell/WMSDK/mw320/boot2/bin/boot2.bin
      ```

      The `flashprog` command initiates the following:
      + Layout – The flashprog utility is first instructed to write a layout to the flash\. The layout is similar to partition information for the flash\. The default layout is located at `/lib/third_party/mcu_vendor/marvell/WMSDK/mw320/sdk/tools/OpenOCD/mw300/layout.txt`\. 
      + Boot2 – This is the boot\-loader used by the WMSDK\. The flashprog command also writes a bootloader to the flash\. It is the bootloader's job to load the microcontroller's firmware image after it is flashed\. Make sure you get the same output as shown in the figure below\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-output-flash-layout.png)

   1. The firmware uses the Wi\-Fi chipset for its functionality, and the Wi\-Fi chipset has its own firmware that must also be present in the flash\. You use the `flashprog.py` utility to flash the Wi\-Fi firmware in the same way that you did to flash the boot2 boot\-loader and the MCU firmware\. Use the following commands to flash the Wi\-Fi firmware\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --wififw ./vendors/marvell/WMSDK/mw320/wifi-firmware/mw30x/mw30x_uapsta_W14.88.36.p135.bin
      ```

      Make sure the output of the command is similar to the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-flash-wifi-firmware.png)

   1.  Use the following commands to flash the MCU firmware\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --mcufw build/cmake/vendors/marvell/mw300_rd/aws_demos.bin -r
      ```

   1. Reset the board\. You should see the logs for the demo app\.

   1. To run the test app, flash the `aws_tests.bin` binary located at the same directory\.

      ```
      cd amzsdk_bundle-x.y.z
      ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --mcufw build/cmake/vendors/marvell/mw300_rd/aws_tests.bin -r
      ```

      Your command output should be similar to the one shown in the figure below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-flash-mcu-firmware.png)

## Debugging<a name="gsg-mw32x-debugging"></a>
+ Start Eclipse and choose **Help** and then choose **Install new software**\. In the **Work with** menu, choose **All Available Sites**\. Enter the filter text `GDB Hardware`\. Select the **C/C\+\+ GDB Hardware Debugging** option and install the plugin\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/mw32x-debugging.png)

## Troubleshooting<a name="gsg-mw32x-troubleshooting"></a>

Network issues  
Check your network credentials\. See "Provisioning" in [Build and run the FreeRTOS demo project](#gsg-mw32x-build-and-run)\.

Enabling additional logs  
+ Enable board specific logs\.

  Enable calls to `wmstdio_init(UART0_ID, 0)` in the function `prvMiscInitialization` in the `main.c` file for tests or demos\.
+ Enabling Wi\-Fi logs

  Enable the macro `CONFIG_WLCMGR_DEBUG` in the  `freertos/vendors/marvell/WMSDK/mw320/sdk/src/incl/autoconf.h` file\. 

Using GDB  
We recommend that you use the `arm-none-eabi-gdb` and `gdb` command files packaged along with the SDK\. Navigate to the directory\.  

```
cd freertos/lib/third_party/mcu_vendor/marvell/WMSDK/mw320
```
Run the following command \(on a single line\) to connect to GDB\.  

```
arm-none-eabi-gdb -x ./sdk/tools/OpenOCD/gdbinit ../../../../../../build/cmake/vendors/marvell/mw300 _rd/aws_demos.axf
```
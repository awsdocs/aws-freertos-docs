# Getting Started with the Marvell MW322 AWS IoT Starter Kit<a name="getting_started_marvell322"></a>

This tutorial provides instructions for getting started with the Marvell MW322 AWS IoT Starter Kit\.

The Marvell MW322 AWS IoT Starter Kit includes the Marvell MW322, a Cortex M4 microcontroller development board that integrates 802\.11b/g/n Wi\-Fi on a single microcontroller chip\. The kit is FCC\-certified and available for sale\. The MW322 module is also FCC\-certified and available for customization and volume sale\.

If you do not have the Marvell MW322 AWS IoT Starter Kit, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0h000000OblKEAS/Marvell-MW322)\.

**Note**  
In this tutorial, we use Ubuntu 16\.04 for developing and debugging applications for the Marvell MW322\. Other operating systems might work, but are not officially supported\.

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the FreeRTOS download directory is referred to as `<freertos>`\.

## Overview<a name="w13aab7c25c23c13"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Set Up Your Development Environment<a name="marvell322-setup-env"></a>

FreeRTOS includes some scripts for installing required third\-party libraries, and for building and flashing applications to the board\. These scripts are in the `vendors/marvell/WMSDK/mw320/sdk` directory\.

The AWS IoT Starter Kit also includes pre\-flashed wireless microcontroller demo project firmware\. 

In addition to the software that is bundled with the M320 AWS IoT Starter Kit and its FreeRTOS port, you must have the following software components installed on your host machine:
+ The GCC toolchain, to cross compile your application\. Versions `4_9_2015q3` and later are supported\.
+ OpenOCD, to access various JTAG functions for programming the board's flash memory, and for downloading and running firmware images in memory\.
+ The CMake build system\. Versions 3\.13 and later are supported\.
+ \(Optional\) A supported IDE, for application development and debugging\.

### Install Required Third\-Party Libraries with installpkgs\.sh<a name="marvell322-sdk"></a>

The `vendors/marvell/WMSDK/mw320/sdk/tools/bin/installpkgs.sh` script attempts to autodetect the machine type and install some required libraries, which include:
+ C libraries
+ A USB library
+ An FTDI library
+ ncurses
+ Python
+ LaTeX

**Note**  
The `installpkgs.sh` script includes instructions for installing packages using `apt-get` for 32\-bit and 64\-bit Ubuntu environments and `yum` for 32\-bit and 64\-bit Fedora environments\. If you have problems running the script on your distribution, open the script file, find the list of required packages, and install them manually\.

With root privileges, issue the following command from the root directory of your FreeRTOS download:

```
./vendors/marvell/WMSDK/mw320/sdk/tools/bin/installpkgs.sh
```

You can configure the permissions on your Linux host machine to allow `flashprog` and `ramload` operations without `sudo`\. To do this, issue the following command:

```
./vendors/marvell/WMSDK/mw320/sdk/tools/bin/perm_fix.sh
```

**Note**  
If you are using the Eclipse IDE, you must configure these permissions\.

### Set Up the Toolchain<a name="marvell322-toolchain"></a>

The FreeRTOS port for the this board is configured to use the GNU toolchain by default\. For the Makefiles to invoke the correct compiler toolchain, the GNU compiler toolchain binaries must be included in the user’s PATH variable\. The GNU toolchain binaries must also be prefixed with `arm-none-eabi-`\.

The GCC toolchain can be used with the GNU Debugger \(GDB\) for debugging with the OpenOCD software that interfaces with JTAG\.

**To set up the GCC toolchain on a Linux machine**

1. Download the toolchain tarball from [launchpad](https://launchpad.net/gcc-arm-embedded/4.9/4.9-2015-q3-update)\. The file name is `gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar.bz2`\.

1. Copy the file to a directory of your choice\. Make sure that there are no spaces in the directory path\.

1. Untar the file with the following command:

   ```
   tar –vxf file-name
   ```

1. Add the toolchain binaries to your system PATH\.

   For example, open the `.profile` file in your `$HOME` directory, and append the following line to the end of the file:

   ```
   PATH="$PATH:<path>/gcc-arm-none-eabit-4_9_2015_q3/bin"
   ```

   Where *<path>* is the full directory path to the `gcc-arm-none-eabit-4_9_2015_q3` folder\.

**Note**  
Some distributions of Ubuntu include a Debian version of the GCC cross compiler\. If your distribution includes a native cross compiler, remove it, and follow the steps to set up the GCC compiler toolchain\.

### Set Up OpenOCD<a name="marvell322-openocd"></a>

OpenOCD version 0\.9 is required\. If an earlier version is installed on your host machine, remove it using your distribution's uninstall process\.

You can install OpenOCD with standard Linux package managers\. For example:

```
apt-get install openocd
```

The default version of OpenOCD installed on your machine depends on the version of your Linux kernel\. 

If you cannot use a package manager to install OpenOCD version 0\.9, follow these steps:

1. Download the OpenOCD version 0\.9 source code from [OpenOCD\.org](http://openocd.org/)\.

1. Extract the `openocd` download, and then change directories to the extracted folder\.

1. Enable FTDI and JLink:

   ```
   ./configure --enable-ftdi --enable-jlink
   ```

1. Make openocd:

   ```
   make install
   ```

### Install CMake<a name="gsg-marvell322-cmake"></a>

The CMake build system is required to build the FreeRTOS demo and test applications for this device\. FreeRTOS supports versions 3\.13 and later\.

You can download the latest version of CMake from [CMake\.org](https://cmake.org/download/)\. Both source and binary distributions are available\.

For more details about using CMake with FreeRTOS, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

## Establish a Serial Connection<a name="marvell322-serial-connect"></a>

**To establish a serial connection between your host machine and your board**

1. Attach one end of a USB cable to your host machine, and the other end to your board\.

   Your host machine should detect the board\. You can issue the dmesg command from the command line, or you can open the `/var/log/messages` file to verify that the board has been detected\. A message similar to the following shows a successful connection:

   ```
   Jan 6 20:00:51 localhost kernel: usb 4-2: new full speed USB device using
   uhci_hcd and address 127
   Jan 6 20:00:51 localhost kernel: usb 4-2: configuration #1 chosen from 1
   choice
   Jan 6 20:00:51 localhost kernel: ftdi_sio 4-2:1.0: FTDI USB Serial Device
   converter detected
   Jan 6 20:00:51 localhost kernel: ftdi_sio: Detected FT2232C
   Jan 6 20:00:51 localhost kernel: usb 4-2: FTDI USB Serial Device converter
   now attached to ttyUSB0
   Jan 6 20:00:51 localhost kernel: ftdi_sio 4-2:1.1: FTDI USB Serial Device
   converter detected
   Jan 6 20:00:51 localhost kernel: ftdi_sio: Detected FT2232C
   Jan 6 20:00:51 localhost kernel: usb 4-2: FTDI USB Serial Device converter
   now attached to ttyUSB1
   ```
**Note**  
Marvell development boards have an FTDI chip that exposes two USB interfaces to the host\. The first interface \(`ttyUSB0`\) is to the MCU's JTAG functionality\. The second interface \(`ttyUSB1`\) is to the MCU's physical UARTx port\.

1. Open a serial connection to the `ttyUSB1` interface with the following settings:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/userguide/getting_started_marvell322.html)

   For example, if you are using minicom, do the following:

   1. Start minicom in setup mode:

      ```
      minicom -s
      ```

   1. Go to **Serial Port Setup**\.

   1. Configure the following settings:

      ```
      | A - Serial Device : /dev/ttyUSB1
      | B – Lockfile Location : /var/lock
      | C - Callin Program :
      | D - Callout Program :
      | E - Bps/Par/Bits : 115200 8N1
      | F – Hardware Flow Control : No
      | G – Software Flow Control : No
      ```

      Go to **Exit** to start showing messages from the serial console\.

   For more information about installing a terminal emulator to set up a serial connection, see [Installing a Terminal Emulator](uart-term.md)\.

## Build, Flash, and Run the FreeRTOS Demo Project<a name="marvell322-build-and-run-example"></a>

You can use CMake and the utility scripts included with the M320 port of FreeRTOS to build, flash, and run the FreeRTOS demo project from the command line\. Or you can use an IDE to build your project\.

### Generate the Demo Build Files with CMake<a name="marvell322-cmake-gen"></a>

Issue the following command from the root of the FreeRTOS download to generate the demo build files with CMake:

```
cmake -DVENDOR=marvell -DBOARD=mw320 -DCOMPILER=arm-gcc -S . -B build -DAFR_ENABLE_TESTS=0
```

or

```
cmake -DVENDOR=marvell -DBOARD=mw322 -DCOMPILER=arm-gcc -S . -B build -DAFR_ENABLE_TESTS=0
```

You should see output similar to the following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-build-output-1.png)

### Build the Demo with make<a name="marvell322-build-demo-make"></a>

Issue the following commands to build the demo:

```
cd build
```

```
make all -j4
```

You should see output similar to the following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-build-output-2.png)

You can use a similar set of commands to build a test project:

```
cmake -DVENDOR=marvell -DBOARD=mw320 -DCOMPILER=arm-gcc -S . -B build -DAFR_ENABLE_TESTS=1
```

or

```
cmake -DVENDOR=marvell -DBOARD=mw322 -DCOMPILER=arm-gcc -S . -B build -DAFR_ENABLE_TESTS=1
```

```
cd build
```

```
make all -j4
```

**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

### Flash the Application<a name="marvell322-flash-app"></a>

The `flashprog.py` script is used to program your board's flash memory\. The script is written in Python 2\.7\.

Before you can flash the demo application image to the board, prepare the board's flash memory with a layout file and the Boot2 bootloader\. 

**To load the layout file and Boot2 bootloader**

1. Change directories to the root of the FreeRTOS download\.

1. Run the `flashprog.py` Python script with the `-l` and `--boot2` options:

   ```
   ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py -l ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/mw300/layout.txt --boot2 ./vendors/marvell/WMSDK/mw320/boot2/bin/boot2.bin
   ```

   The `flashprog` script writes a layout to the flash, according to the default layout configuration defined in `vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/mw300/layout.txt`\. The layout holds partitioned information about the flash\.

   The script also writes a bootloader to the flash\. The bootloader is located at `vendors/marvell/WMSDK/mw320/sdk/boot2/bin/boot2.bin`\. The bootloader loads the microcontroller’s firmware image after it is flashed to the board\.

   You should see output similar to the following:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-flashprog.png)

After you flash the layout file and bootloader to the board, flash some firmware to the board\. The Wi\-Fi chipset requires that its own firmware is present in flash memory\.

**To flash the Wi\-Fi firmware**

1. Change directories to the root of the FreeRTOS download\.

1. Run the `flashprog.py` Python script with the `--wififw` option:

   ```
   ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --wififw ./vendors/marvell/WMSDK/mw320/wifi-firmware/mw30x/mw30x_uapsta_W14.88.36.p135.bin
   ```

   The `flashprog` script flashes the firmware to the board\.

   You should see output similar to the following:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-flashprog-wifi.png)

With the layout, bootloader, and Wi\-Fi firmware flashed to the board, you can flash the demo application to the board and run it\.

**To flash and run the demo**

1. Change directories to the root of the FreeRTOS download\.

1. Run the `flashprog.py` Python script with the `--mcufw` and `-r` options:

   ```
   ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/flashprog.py --mcufw ./build//marvell/mw320/aws_demos.bin -r
   ```

   The `flashprog` script flashes the demo to the board\. Running the script with the `-r` option resets the board\.

   Reset the board\.

   You should see logs for the demo application\. The output should be similar to the following:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-flashprog-demo.png)
**Note**  
To flash the `aws_tests` application, use the same command, but specify the `aws_tests.bin` file instead of `aws_demos.bin`\.  
If you are only changing the application, you don't need to reload the layout, bootloader, and Wi\-Fi firmware\. If you change the layout, you might need to reload all of the components\.

When you build, flash, and run the demo, you should see output similar to the following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-demo-output.png)

#### Monitoring MQTT Messages on the Cloud<a name="marvell322-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="marvell322-troubleshooting"></a>

### Connecting to the GNU Debugger<a name="marvell322-gnu-debug"></a>

**To connect to the GNU Debugger \(GDB\)**

1. Change directories:

   ```
   cd <freertos>/vendors/marvell/WMSDK/mw320
   ```

1. Connect to GDB with the arm\-none\-eabi\-gdb command:

   ```
   arm-none-eabi-gdb -x ./sdk/tools/OpenOCD/gdbinit ../../../../build/vendors/marvell/boards/mw300_rd/aws_demos.axf
   ```

   If you are debugging a FreeRTOS test application, target `aws_tests.axf` instead\.

### Loading the Application to SRAM<a name="marvell322-load-sram"></a>

You can load the demo to your device's static random\-access memory \(SRAM\) and then execute the application on your device with the `ramload.py` script\. Using `ramload.py` to load and execute the application is a faster operation than loading to flash memory with the `flashprog.py` script, making it a more efficient approach to iterative development\.

**Note**  
The `ramload.py` script is written in Python 2\.7\.

**To load to SRAM**

1. Change directories to the root of the FreeRTOS download\.

1. Run the `ramload.py` Python script on the `aws_demos.axf` file:

   ```
   ./vendors/marvell/WMSDK/mw320/sdk/tools/OpenOCD/ramload.py ./build/vendors/marvell/boards/mw300_rd/aws_demos.axf
   ```

   You should see logs for the demo application\. The output should be similar to the following:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/marvell-ramload.png)

**Note**  
Images loaded to SRAM are erased on reboot\.

### Enabling Other Logs<a name="marvell322-other-logs"></a>

You might need to enable other logging messages to troubleshoot problems that you encounter while getting started with this board\.

**To enable board\-specific logs**

1. Open the `main.c` file of the project that you are working in \(for example, `aws_tests` or `aws_demos`\)\.

1. Enable the call to `wmstdio_init(UART0_ID, 0)` in the function `prvMiscInitialization`\.

**To enable Wi\-Fi logs**

1. Open `vendors/marvell/WMSDK/mw320/sdk/src/incl/autoconf.h`\.

1. Enable the macro `CONFIG_WLCMGR_DEBUG`\.

### Using an IDE Development and Debugging<a name="marvell322-use-ide"></a>

#### Set Up an IDE<a name="marvell322-setup-ide"></a>

You can use an IDE for developing and debugging applications, and for visualizing your projects\.

If you are using the Eclipse IDE, for example, use the `perm_fix.sh` script to configure some permissions:

```
./vendors/marvell/WMSDK/mw320/tools/bin/perm_fix.sh
```

**To set up Eclipse**

1. Install Java Run Time Environment \(JRE\) from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)\.

   JRE is required to run Eclipse\. The JRE version \(32\-bit or 64\-bit\) must match the version of Eclipse \(32\-bit or 64\-bit\) that you install\.

1. Download Eclipse IDE for C/C\+\+ Developers from [Eclipse\.org](http://www.eclipse.org)\. Eclipse versions 4\.9\.0 and later are supported\.

1. Extract the downloaded archive folder, and then run the platform\-specific Eclipse executable to start the IDE\.

#### Build the Demo with an IDE<a name="marvell322-build-demo-ide"></a>

You can open and build the demo project's build files in your IDE instead of building the demo directly from the command line with `make`\. Opening the files in an IDE can help you visualize the project before you build it\.

**Note**  
You must generate the build files with the cmake command every time you switch between the `aws_demos` project and the `aws_tests` project\.

**To build the project with Eclipse**

1. Open Eclipse\.

1. Choose your workspace to create a project\.

1. On the **Select a wizard** page, expand **C/C\+\+**, and choose **Makefile Project with Existing Code**\.

1. On the **Import existing code** page, browse to the location of the `aws_demos` source code, choose `aws_demos`, and then choose **Finish**\.

1. From the **Project Explorer**, right\-click `aws_demos`, and then build the project\.

   A successful build generates the `aws_demos.bin` executable\.

For general troubleshooting information about Getting Started with FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.
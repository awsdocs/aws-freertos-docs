# Getting Started with Amazon FreeRTOS<a name="freertos-getting-started"></a>

This section shows you how to download and configure Amazon FreeRTOS and run a demo application on one of the qualified microcontroller boards\. In this tutorial, we assume you are familiar with AWS IoT and the AWS IoT console\. If not, we recommend that you start with the [AWS IoT Getting Started](http://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html) tutorial\.

## Debugging Code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT<a name="debugging-espressif"></a>

You need a JTAG to USB cable\. We use a USB to MPSSE cable \(for example, the [FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Products/Cables/USBMPSSE.htm)\)\. 

### ESP\-DevKitC JTAG Setup<a name="jtag-devkitc"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32 DevkitC:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  | TMS | 
|  Yellow \(pin 3\)  |  IO12  | TDI | 
| Black \(pin 10\) | GND | GND | 
| Orange \(pin 2\) | IO13 | TCK | 
| Green \(pin 4\) | IO15 | TDO | 

### ESP\-WROVER\-KIT JTAG Setup<a name="jtag-wrover"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32\-WROVER\-KIT:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  |  TMS  | 
|  Yellow \(pin 3\)  |  IO12  |  TDI  | 
|  Orange \(pin 2\)  |  IO13  |  TCK  | 
|  Green \(pin 4\)  |  IO15  |  TDO  | 

These tables were developed from the FTDI C232HM\-DDHSL\-0 [datasheet](http://www.ftdichip.com/Support/Documents/DataSheets/Cables/DS_C232HM_MPSSE_CABLE.PDF)\. For more information, see C232HM MPSSE Cable Connection and Mechanical Details in the datasheet\.

To enable JTAG on the ESP\-WROVER\-KIT, place jumpers on the TMS, TDO, TDI, TCK, and S\_TDI pins as shown here:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/JP8-jumpers.png)

### Debugging on Windows<a name="debugging-espressif-windows"></a>

**To set up for debugging on Windows**

1. Connect the USB side of the FTDI C232HM\-DDHSL\-0 to your computer and the other side as described in [Debugging Code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT](#debugging-espressif)\. The FTDI C232HM\-DDHSL\-0 device should appear in **Device Manager** under **Universal Serial Bus Controllers**\.

1. From the list of USB controllers, right\-click the FTDI C232HM\-DDHSL\-0 device \(the manufacturer is FTDI\), and choose **Properties**\. In the properties window, choose the **Details** tab to see the properties of the device\. If the device is not listed, install the [Windows driver for FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Drivers/D2XX.htm)\.

1. Verify that the vendor ID and product ID displayed in **Device Manager** match the IDs in `demos\espressif\esp32_devkitc_esp_wrover_kit\esp32_devkitj_v1.cfg`\. The IDs are specified in a line that begins with `ftdi_vid_pid` followed by a vendor ID and a product ID:

   ```
   ftdi_vid_pid 0x0403 0x6014
   ```

1. Download [OpenOCD for Windows](https://github.com/espressif/openocd-esp32/releases)\.

1. Unzip the file to `C:\` and add `C:\openocd-esp32\bin` to your system path\.

1. OpenOCD requires libusb, which is not installed by default on Windows\. To install it:

   1. Download [zadig\.exe](https://zadig.akeo.ie)\.

   1. Run `zadig.exe`\. From the **Options** menu, choose **List All Devices**\.

   1. From the drop\-down menu, choose **C232HM\-DDHSL\-0**\.

   1. In the target driver box, to the right of the green arrow, choose **WinUSB**\.

   1. From the drop\-down box under the target driver box, choose the arrow, and then choose **Install Driver**\. Choose **Replace Driver**\.

1. Open a command prompt, navigate to `<BASE_FOLDER>\demos\espressif\esp32_devkitc_esp_wrover_kit\make` and run:

   ```
   openocd.exe -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

   Leave this command prompt open\.

1. Open a new command prompt, navigate to your `msys32` directory, and run `mingw32.exe`\. In the mingw32 terminal, navigate to `<BASE_FOLDER>\demos\espressif\esp32_devkitc_esp_wrover_kit\make` and run make flash monitor\.

1. Open another mingw32 terminal, navigate to `<BASE_FOLDER>\demos\espressif\esp32_devkitc_esp_wrover_kit\make` and run `xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf`\. The program should stop in the `main` function\.

**Note**  
The ESP32 supports a maximum of two break points\.

### Debugging on macOS<a name="debugging-espressif-macos"></a>

1. Download the [FTDI driver for macOS](http://www.ftdichip.com/Drivers/VCP.htm)\.

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\.

1. Extract the downloaded \.tar file and set the path in `.bash_profile` to `<OCD_INSTALL_DIR>/openocd-esp32/bin`\.

1. Use the following command to install `libusb` on macOS:

   ```
   brew install libusb
   ```

1. Use the following command to unload the serial port driver:

   ```
   sudo kextunload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. If you are running a macOS version later than 10\.9, use the following command to unload Apple's FTDI driver:

   ```
   sudo kextunload -b com.apple.driver.AppleUSBFTDI
   ```

1. Use the following command to get the product ID and vendor ID of the FTDI cable\. It lists the attached USB devices:

   ```
   system_profiler SPUSBDataType
   ```

   The output from `system_profiler` should look like the following:

   ```
   C232HM-DDHSL-0:
   Product ID: 0x6014
   Vendor ID: 0x0403 (Future Technology Devices International Limited)
   ```

1. Verify the vendor and product IDs match the IDs in `demos/espressif/esp32_devkitc_esp_wrover_kit/esp32_devkitj_v1.cfg`\. The IDs are specified on a line that begins with `ftdi_vid_pid` followed by a vendor ID and a product ID:

   ```
   ftdi_vid_pid 0x0403 0x6014
   ```

1. Open a terminal window, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and use the following command to run OpenOCD:

   ```
   openocd -f esp32_devkitj_v1.cfg -f
   								esp-wroom-32.cfg
   ```

1. Open a new terminal, and use the following command to load the FTDI serial port driver:

   ```
   sudo kextload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. Navigate to `<BASE_FOLDER>>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   make flash monitor
   ```

1. Open another new terminal, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf
   ```

   The program should stop at `main()`\.

### Debugging on Linux<a name="debugging-espressif-linux"></a>

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\. Extract the tarball and follow the installation instructions in the readme file\.

1. Use the following command to install libusb on Linux:

   ```
   sudo apt-get install libusb-1.0
   ```

1. Open a terminal and enter **ls \-l /dev/ttyUSB\***to list all USB devices connected to your computer\. This helps you check if the board’s USB ports are recognized by the operating system\. You should see output similar to the following:

   ```
   $ls -l /dev/ttyUSB*
   crw-rw----	1	root	dialout	188,	0	Jul	10	19:04	/dev/ttyUSB0
   crw-rw----	1	root	dialout	188,	1	Jul	10	19:04	/dev/ttyUSB1
   ```

1. Sign off and then sign in and cycle the power to the board to make the changes take effect\. In a terminal prompt, list the USB devices\. Make sure the group\-owner has changed from `dialout` to `plugdev`:

   ```
   $ls -l /dev/ttyUSB*
   crw-rw----	1	root	plugdev	188,	0	Jul	10	19:04	/dev/ttyUSB0
   crw-rw----	1	root	plugdev	188,	1	Jul	10	19:04	/dev/ttyUSB1
   ```

   The `/dev/ttyUSBn` interface with the lower number is used for JTAG communication\. The other interface is routed to the ESP32’s serial port \(UART\) and is used for uploading code to the ESP32’s flash memory\.

1. In a terminal window, navigate to `<BASE_FOLDER>/demos/espressif/ esp32_devkitc_esp_wrover_kit/make`, and use the following command to run OpenOCD:

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

1. Open another terminal, navigate to `<BASE_FOLDER>/demos/espressif/ esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   make flash monitor
   ```

1. Open another terminal, navigate to `<BASE_FOLDER>/demos/espressif/ esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf
   ```

   The program should stop in `main()`\.
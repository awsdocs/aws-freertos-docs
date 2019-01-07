# Getting Started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT<a name="getting_started_espressif"></a>

If you don't have an Espressif ESP32\-DevKitC, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtjUAH/ESP32-DevKitC)\. If you don't have an Espressif ESP32=WROVER\-KIT, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtlUAH/ESP-WROVER-KIT)\. Both devices are supported on Amazon FreeRTOS\. To check which development module you have, see [ESP32 Modules and Boards](https://docs.espressif.com/projects/esp-idf/en/latest/hw-reference/modules-and-boards.html)\.

**Note**  
Currently, the Amazon FreeRTOS port for ESP32\-WROVER\-KIT and ESP DevKitC does not support the following:  
Lightweight IP\.
Symmetric multiprocessing \(SMP\)\.

## Setting Up the Espressif Hardware<a name="setup-hw-espressif"></a>

For the ESP32\-DevKitC development board, see [Getting Started with the ESP32\-DevKitC development board](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-devkitc.html)\.

For the ESP\-WROVER\-KIT development board, see [Getting Started with the ESP\-WROVER\-KIT development board](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-wrover-kit.html)\.

## Setting Up Your Environment<a name="setup-env-esspressif"></a>

### Establishing a Serial Connection<a name="establish-serial-connection"></a>

To establish a serial connection with the ESP32\-DevKitC, you must install CP210x USB to UART Bridge VCP drivers\. If you are running Windows 10, install v6\.7\.5 of the CP210x USB to UART Bridge drivers\. If you are running an older version of Windows, install the version indicated in the list of downloads\.

To establish a serial connection with the ESP32\-WROVER\-KIT, you must install some FTDI virtual COM port drivers\. For more information, see [Establishing a Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html)\.

Make a note of the serial port you configure \(based on host OS\)\. You need it during the build process\.

### Setting Up the Toolchain<a name="setup-toolchain"></a>

When following the links below, do not install the ESP\-IDF library from Espressif, Amazon FreeRTOS already contains this library\. In addition, make sure the `IDF_PATH` environment variable has not been set\.
+ [Setting up the toolchain for Windows]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/windows-setup.html)
+ [Setting up the toolchain for macOS]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/macos-setup.html)
+ [Setting up the toolchain for Linux]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/linux-setup.html)

## Download and Configure Amazon FreeRTOS<a name="download-and-configure-espressif"></a>

After your environment is set up, you can download Amazon FreeRTOS\.

### Downloading Amazon FreeRTOS<a name="download-espressif"></a>

Clone the `amazon-freertos` repository from [GitHub](https://github.com/aws/amazon-freertos)\.

In this tutorial, the path to the `amazon-freertos` directory is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `amazon-freertos` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\amazon-freertos` works, but `C:\Users\Username\Documents\Development\Projects\amazon-freertos` causes build failures\.

### Configure Your Project<a name="config-project-espressif"></a>

1. If you are running macOS or Linux, open a terminal prompt\. If you are running Windows, open mingw32\.exe\.

1. Install Python 2\.7\.10 or later\. 

1. If you are running Windows, use the easy\_install awscli to install the AWS CLI in the mingw32 environment\. 

   If you are running macOS or Linux, make sure the AWS CLI is installed on your system\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Run aws configure to configure the AWS CLI\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

1. Use the following command to install the boto3 Python module:
   + On Windows, in the mingw32 environment, use easy\_install boto3
   + On macOS or Linux, use pip install boto3

Amazon FreeRTOS includes scripts to make it easier to set up your Espressif board\. To configure the Espressif scripts, open `<BASE_FOLDER>/tools/aws_config_quick_start/configure.json` and set the following attributes:

`afr_source_dir`  
The complete path to the `amazon-freertos` directory on your computer\. Make sure that you use forward slashes to specify this path\.

`thing_name`  
The name of the IoT thing that represents your board\.

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

1. Go to the `<BASE_FOLDER>/tools/aws_config_quick_start` directory and run the following command:

   python SetupAWS\.py setup

This script creates an IoT thing, certificate, and policy\. It attaches the IoT policy to the certificate and the certificate to the IoT thing\. It also populates the `aws_clientcredential.h` file with your AWS IoT endpoint, Wi\-Fi SSID, and credentials\. Finally, it formats your certificate and private key and writes them to the `aws_clientcredential.h` header file\. For more information about the script, see the README\.md in the `<BASE_FOLDER>/tools/aws_config_quick_start` directory\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="build-and-run-example-espressif"></a>

**To flash the demo application onto your board**

1. Connect your board to your computer\.

1. In macOS or Linux, open a terminal\. In Windows, open mingw32\.exe \(downloaded from mysys toolchain\)\.

1. Navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make` and enter following command:

   ```
   make menuconfig
   ```

   In the Espressif IoT Development Framework Configuration menu, navigate to **Serial flasher config**\.

   To confirm a selection in the menu, choose **Select**\. To save a configuration, choose **Save**\. To exit the Espressif IoT Development Framework Configuration menu, choose **Exit**\.

   Choose **Default serial port** to configure the serial port\. The serial port you configure here is used to write the demo application to your board\. On Windows, serial ports have names like `COM1`\. On macOS, they start with `/dev/cu`\. On Linux, they start with `/dev/tty`\.

   Choose **Default baud rate** to change the default baud rate to use while communicating with your board\. Increasing the baud rate can reduce the time required to flash your board\. Depending on your hardware, you can increase the default baud rate up to 921600\.

To build and flash firmware \(including boot loader and partition table\) and monitor serial console output, open a command prompt\. Navigate to `<BASE_FOLDER>\demos\espressif\esp32_devkitc_esp_wrover_kit/make` and run the following command:

```
make flash monitor
```

At the end of the compilation output, you should see text like the following:

```
I (31) boot: ESP-IDF v3.1-dev-322-gf307f41-dirty 2nd stage bootloader
I (31) boot: compile time 11:30:50
I (34) boot: Enabling RNG early entropy source...
I (37) boot: SPI Speed      : 40MHz
I (41) boot: SPI Mode       : DIO
I (45) boot: SPI Flash Size : 4MB
I (49) boot: Partition Table:
I (53) boot: ## Label            Usage          Type ST Offset   Length
I (60) boot:  0 nvs              WiFi data        01 02 00009000 00006000
I (68) boot:  1 phy_init         RF data          01 01 0000f000 00001000
I (75) boot:  2 factory          factory app      00 00 00010000 00100000
I (82) boot:  3 storage          Unknown data     01 82 00110000 00010000
I (90) boot: End of partition table
I (94) esp_image: segment 0: paddr=0x00010020 vaddr=0x3f400020 size=0x12710 ( 75536) map
I (129) esp_image: segment 1: paddr=0x00022738 vaddr=0x3ffb0000 size=0x0240c (  9228) load
I (133) esp_image: segment 2: paddr=0x00024b4c vaddr=0x40080000 size=0x00400 (  1024) load
0x40080000: _iram_start at BASE_FOLDER/AmazonFreeRTOS-Espressif/lib/FreeRTOS/portable/GCC/Xtensa_ESP32/xtensa_vectors.S:1685

I (136) esp_image: segment 3: paddr=0x00024f54 vaddr=0x40080400 size=0x0b0bc ( 45244) load
I (164) esp_image: segment 4: paddr=0x00030018 vaddr=0x400d0018 size=0x6d454 (447572) map
0x400d0018: _stext at ??:?

I (319) esp_image: segment 5: paddr=0x0009d474 vaddr=0x4008b4bc size=0x02d44 ( 11588) load
0x4008b4bc: xStreamBufferSend at BASE_FOLDER/AmazonFreeRTOS-Espressif/lib/FreeRTOS/stream_buffer.c:636

I (324) esp_image: segment 6: paddr=0x000a01c0 vaddr=0x400c0000 size=0x00000 (     0) load
I (334) boot: Loaded app from partition at offset 0x10000
I (334) boot: Disabling RNG early entropy source...
I (338) cpu_start: Pro cpu up.
I (341) cpu_start: Single core mode
I (346) heap_init: Initializing. RAM available for dynamic allocation:
I (353) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM
I (359) heap_init: At 3FFC0420 len 0001FBE0 (126 KiB): DRAM
I (365) heap_init: At 3FFE0440 len 00003BC0 (14 KiB): D/IRAM
I (371) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM
I (378) heap_init: At 4008E200 len 00011E00 (71 KiB): IRAM
I (384) cpu_start: Pro cpu start user code
I (66) cpu_start: Starting scheduler on PRO CPU.
I (96) wifi: wifi firmware version: f79168c
I (96) wifi: config NVS flash: enabled
I (96) wifi: config nano formating: disabled
I (106) system_api: Base MAC address is not set, read default base MAC address from BLK0 of EFUSE
I (106) system_api: Base MAC address is not set, read default base MAC address from BLK0 of EFUSE
I (136) wifi: Init dynamic tx buffer num: 32
I (136) wifi: Init data frame dynamic rx buffer num: 32
I (136) wifi: Init management frame dynamic rx buffer num: 32
I (136) wifi: wifi driver task: 3ffc5ec4, prio:23, stack:4096
I (146) wifi: Init static rx buffer num: 10
I (146) wifi: Init dynamic rx buffer num: 32
I (156) wifi: wifi power manager task: 0x3ffcc248 prio: 21 stack: 2560
0 7 [Tmr Svc] WiFi module initialized. Connecting to AP Guest...
W (166) phy_init: failed to load RF calibration data (0x1102), falling back to full calibration
I (396) phy: phy_version: 383.0, 79a622c, Jan 30 2018, 15:38:06, 0, 2
I (406) wifi: mode : sta (30:ae:a4:4b:3d:64)
I (406) WIFI: SYSTEM_EVENT_STA_START
I (526) wifi: n:1 0, o:1 0, ap:255 255, sta:1 0, prof:1
I (526) wifi: state: init -> auth (b0)
I (536) wifi: state: auth -> assoc (0)
I (536) wifi: state: assoc -> run (10)
I (536) wifi: connected with Guest, channel 1
I (536) WIFI: SYSTEM_EVENT_STA_CONNECTED
I (3536) wifi: pm start, type:0

1 826 [IP-task] vDHCPProcess: offer c0a8520bip
I (8356) event: sta ip: 192.168.82.11, mask: 255.255.224.0, gw: 192.168.64.1
I (8356) WIFI: SYSTEM_EVENT_STA_GOT_IP
2 827 [IP-task] vDHCPProcess: offer c0a8520bip
3 828 [Tmr Svc] WiFi Connected to AP. Creating tasks which use network...
4 828 [Tmr Svc] Creating MQTT Echo Task...
5 829 [MQTTEcho] MQTT echo attempting to connect to a14o5vz6c0ikzv.iot.us-west-2.amazonaws.com.
6 829 [MQTTEcho] Sending command to MQTT task.
7 830 [MQTT] Received message 10000 from queue.
8 2030 [IP-task] Socket sending wakeup to MQTT task.
I (20416) PKCS11: Initializing SPIFFS
W (20416) SPIFFS: mount failed, -10025. formatting...
I (20956) PKCS11: Partition size: total: 52961, used: 0
9 2596 [MQTT] Received message 0 from queue.
10 2601 [IP-task] Socket sending wakeup to MQTT task.
11 2601 [MQTT] Received message 0 from queue.
12 2607 [IP-task] Socket sending wakeup to MQTT task.
13 2607 [MQTT] Received message 0 from queue.
14 2607 [MQTT] MQTT Connect was accepted. Connection established.
15 2607 [MQTT] Notifying task.
16 2608 [MQTTEcho] Command sent to MQTT task passed.
17 2608 [MQTTEcho] MQTT echo connected.
18 2608 [MQTTEcho] MQTT echo test echoing task created.
19 2608 [MQTTEcho] Sending command to MQTT task.
20 2609 [MQTT] Received message 20000 from queue.
21 2610 [IP-task] Socket sending wakeup to MQTT task.
22 2611 [MQTT] Received message 0 from queue.
23 2612 [IP-task] Socket sending wakeup to MQTT task.
24 2612 [MQTT] Received message 0 from queue.
25 2612 [MQTT] MQTT Subscribe was accepted. Subscribed.
26 2612 [MQTT] Notifying task.
27 2613 [MQTTEcho] Command sent to MQTT task passed.
28 2613 [MQTTEcho] MQTT Echo demo subscribed to freertos/demos/echo
29 2613 [MQTTEcho] Sending command to MQTT task.
30 2614 [MQTT] Received message 30000 from queue.
31 2619 [IP-task] Socket sending wakeup to MQTT task.
32 2619 [MQTT] Received message 0 from queue.
33 2620 [IP-task] Socket sending wakeup to MQTT task.
34 2620 [MQTT] Received message 0 from queue.
35 2620 [MQTT] MQTT Publish was successful.
36 2620 [MQTT] Notifying task.
37 2620 [MQTTEcho] Command sent to MQTT task passed.
38 2620 [MQTTEcho] Echo successfully published 'Hello World 0'
39 2624 [IP-task] Socket sending wakeup to MQTT task.
40 2624 [MQTT] Received message 0 from queue.
41 2624 [Echoing] Sending command to MQTT task.
42 2624 [MQTT] Received message 40000 from queue.
43 2625 [IP-task] Socket sending wakeup to MQTT task.
44 2625 [MQTT] Received message 0 from queue.
45 2626 [IP-task] Socket sending wakeup to MQTT task.
46 2626 [MQTT] Received message 0 from queue.
47 2628 [IP-task] Socket sending wakeup to MQTT task.
48 2628 [MQTT] Received message 0 from queue.
49 2628 [MQTT] MQTT Publish was successful.
50 2628 [MQTT] Notifying task.
51 2628 [Echoing] Command sent to MQTT task passed.
52 2630 [Echoing] Message returned with ACK: 'Hello World 0 ACK'

*** Similar output deleted for brevity ***

317 7692 [IP-task] Socket sending wakeup to MQTT task.
318 7692 [MQTT] Received message 0 from queue.
319 7698 [IP-task] Socket sending wakeup to MQTT task.
320 7698 [MQTT] Received message 0 from queue.
321 8162 [MQTTEcho] Sending command to MQTT task.
322 8162 [MQTT] Received message 190000 from queue.
323 8163 [IP-task] Socket sending wakeup to MQTT task.
324 8163 [MQTT] Received message 0 from queue.
325 8164 [IP-task] Socket sending wakeup to MQTT task.
326 8164 [MQTT] Received message 0 from queue.
327 8164 [MQTT] MQTT Publish was successful.
328 8164 [MQTT] Notifying task.
329 8165 [MQTTEcho] Command sent to MQTT task passed.
330 8165 [MQTTEcho] Echo successfully published 'Hello World 11'
331 8167 [IP-task] Socket sending wakeup to MQTT task.
332 8167 [MQTT] Received message 0 from queue.
333 8168 [Echoing] Sending command to MQTT task.
334 8169 [MQTT] Received message 1a0000 from queue.
335 8170 [IP-task] Socket sending wakeup to MQTT task.
336 8170 [MQTT] Received message 0 from queue.
337 8171 [IP-task] Socket sending wakeup to MQTT task.
338 8171 [MQTT] Received message 0 from queue.
339 8171 [MQTT] MQTT Publish was successful.
340 8171 [MQTT] Notifying task.
341 8172 [Echoing] Command sent to MQTT task passed.
342 8173 [Echoing] Message returned with ACK: 'Hello World 11 ACK'
343 8174 [IP-task] Socket sending wakeup to MQTT task.
344 8174 [MQTT] Received message 0 from queue.
345 8179 [IP-task] Socket sending wakeup to MQTT task.
346 8179 [MQTT] Received message 0 from queue.
347 8665 [MQTTEcho] Sending command to MQTT task.
348 8665 [MQTT] Received message 1b0000 from queue.
349 8665 [MQTT] About to close socket.
350 8666 [IP-task] Socket sending wakeup to MQTT task.
351 8667 [MQTT] Socket closed.
352 8667 [MQTT] Stack high watermark for MQTT task: 2792
353 8667 [MQTT] Notifying task.
354 8667 [MQTT] Received message 0 from queue.
355 8668 [MQTTEcho] Command sent to MQTT task passed.
356 8668 [MQTTEcho] MQTT echo demo finished.
```

### Run the Bluetooth Low\-Energy Demos<a name="espressif-run-ble"></a>


|  | 
| --- |
| Amazon FreeRTOS support for Bluetooth Low Energy is in public beta release\. BLE demos are subject to change\. | 

Amazon FreeRTOS supports [Bluetooth Low Energy \(BLE\)](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-library.html) connectivity\. You can download Amazon FreeRTOS with BLE from [GitHub](https://github.com/aws/amazon-freertos/tree/feature/ble-beta)\. The Amazon FreeRTOS BLE library is still in public beta, so you need to switch branches to access the code for your board\. Check out the branch named `feature/ble-beta`\.

To run the Amazon FreeRTOS demo project across BLE, you need to run the Amazon FreeRTOS BLE Mobile SDK Demo Application on an iOS or Android mobile device\.

**To set up the the Amazon FreeRTOS BLE Mobile SDK Demo Application**

1. Follow the instructions in [Mobile SDKs for Amazon FreeRTOS Bluetooth Devices](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-mobile.html) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [Amazon FreeRTOS BLE Mobile SDK Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-sdk-app) to set up the demo mobile application on your mobile device\.

For instructions about how to run the MQTT over BLE demo on your board, see the [MQTT over BLE demo application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt)\.

For instructions about how to run the Wi\-Fi Provisioning demo on your board, see the [Wi\-Fi Provisioning demo application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-wifi)\.

## Troubleshooting<a name="getting_started_espressif_troubleshooting"></a>
+ If you are using a Mac and it does not recognize your ESP\-WROVER\-KIT, make sure you do not have the D2XX drivers installed\. To uninstall them, follow the instructions in the [FTDI Drivers Installation Guide for macOS X](http://www.ftdichip.com/Support/Documents/AppNotes/AN_134_FTDI_Drivers_Installation_Guide_for_MAC_OSX.pdf)\.
+ The monitor utility provided by ESP\-IDF \(and invoked using make monitor\) helps you decode addresses\. For this reason, it can help you get some meaningful backtraces in the event the application crashes\. For more information, see [Automatically Decoding Addresses]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/idf-monitor.html#automatically-decoding-addresses) on the Espressif website\.
+ It is also possible to enable GDBstub for communication with gdb without requiring any special JTAG hardware\. For more information, see [Launch GDB for GDBStub]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/idf-monitor.html#launch-gdb-for-gdbstub)\.
+ For information about setting up an OpenOCD\-based environment if JTAG hardware\-based debugging is required, see [JTAG Debugging]( https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/jtag-debugging)\.
+ If `pyserial` cannot be installed using `pip` on macOS, download it from [pyserial](https://pypi.org/simple/pyserial)\.
+ If the board resets continuously, try erasing the flash by entering the following command on the terminal:

  ```
  make erase_flash
  ```
+ If you see errors when you run `idf_monitor.py`, use Python 2\.7\.

**Other Notes**
+ Required libraries from ESP\-IDF are included in Amazon FreeRTOS, so there is no need to download them externally\. If the `IDF_PATH` environment variable is set, we recommend that you clear it before you build Amazon FreeRTOS\. 
+ On Window systems, it can take 3\-4 minutes for the project to build\. You can use the `-j4` switch on the make command to reduce the build time:

  ```
  make flash monitor -j4
  ```

### Debugging Code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT<a name="debugging-espressif"></a>

You need a JTAG to USB cable\. We use a USB to MPSSE cable \(for example, the [FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Products/Cables/USBMPSSE.htm)\)\. 

#### ESP\-DevKitC JTAG Setup<a name="jtag-devkitc"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32 DevkitC:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  | TMS | 
|  Yellow \(pin 3\)  |  IO12  | TDI | 
| Black \(pin 10\) | GND | GND | 
| Orange \(pin 2\) | IO13 | TCK | 
| Green \(pin 4\) | IO15 | TDO | 

#### ESP\-WROVER\-KIT JTAG Setup<a name="jtag-wrover"></a>

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

#### Debugging on Windows<a name="debugging-espressif-windows"></a>

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

#### Debugging on macOS<a name="debugging-espressif-macos"></a>

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
   DEVICE:
   							
   Product ID: product-ID
   Vendor ID: vendor-ID (Future Technology Devices International Limited)
   ```

1. Open `demos/espressif/esp32_devkitc_esp_wrover_kit/esp32_devkitj_v1.cfg`\. The vendor ID and product ID for your device are specified in a line that begins with `ftdi_vid_pid`\. Change the IDs to match the IDs from the `system_profiler` output in the previous step\.

1. Open a terminal window, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and use the following command to run OpenOCD:

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
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

#### Debugging on Linux<a name="debugging-espressif-linux"></a>

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
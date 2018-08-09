# Amazon FreeRTOS Developer Guide<a name="freertos-dev-guide"></a>

This section contains information required for writing embedded applications with Amazon FreeRTOS\.

**Topics**
+ [Amazon FreeRTOS Architecture](freertos-architecture.md)
+ [FreeRTOS Kernel Fundamentals](dev-guide-freertos-kernel.md)
+ [Amazon FreeRTOS Libraries](dev-guide-freertos-libraries.md)
+ [Amazon FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)
+ [Amazon FreeRTOS Console User Guide](freertos-ocw-ug.md)
+ [Downloading Amazon FreeRTOS from GitHub](freertos-mds-projects-github.md)
+ [Amazon FreeRTOS Qualification Program](freertos-qualification-program.md)
+ [Supported Platforms](#supported-platforms)

## Supported Platforms<a name="supported-platforms"></a>

### Texas Instruments CC3220SF\-LAUNCHXL<a name="ti"></a>

The SimpleLink Wi\-Fi CC3220SF\-LAUNCHXL LaunchPad Development Kit includes the CC3220SF, a single\-chip wireless microcontroller \(MCU\) with ARM Cortex \-M4 Core at 80 MHz, 1 MB Flash, 256 KB of RAM, and enhanced security features\. The on\-chip Wi\-Fi module offloads TLS and TCP/IP processing, freeing up memory and compute for the application on the main microcontroller\. For more information about this platform, see, [Texas Instruments CC3220SF\-LAUNCHXL](http://www.ti.com/tool/cc3220sf-launchxl)\.

### STMicroelectronics STM32L4 Discovery Kit – IoT Node<a name="stm"></a>

The STM32L4 IoT Discovery Kit \(B\-L475E\-IOT01A\) supports Wi\-Fi and integrates additional sensors\. The kit has an STM32L4 Series MCU based on ARM Cortex \-M4 core at 80 MHz with 1 MB of flash memory and 128 KB of SRAM, and Wi\-Fi module Inventek ISM43362\-M3G\-L44\. The Wi\-Fi module offloads TCP/IP processing from the MCU\. The interface to the Wi\-Fi module for this kit has not yet been optimized for use with Amazon FreeRTOS, so there are limitations on its use\. We recommend using the Secure Sockets APIs from low\-priority tasks only, and to limit transmit throughput\. Revisions to improve this interface are planned in the future\. For more information about this platform, see [STMicroelectronicsSTM32L4Discovery Kit IoT Node](http://www.st.com/en/evaluation-tools/b-l475e-iot01a.html)\.

### NXP LPC54108 IoT Module<a name="nxp"></a>

The LPC54018 IoT Module from NXP includes an LPC54018 MCU with a 180MHz ARM Cortex\-M4 core with 360 KB of SRAM, 128 MB of Quad\-SPI Flash \(Macronix MX25L12835FM2\), and a Longsys IEEE802\.11b/g/n Wi\-Fi module based on Qualcomm GT1216\. The Wi\-Fi module offloads TCP/IP processing from the MCU\. The LPC54018 IoT Module requires a debugger and J\-Link connector \(available in the NXP Direct store\) or a baseboard\. For more information about this platform, see [NXP LPC54018 IoT Module](https://www.nxp.com/support/developer-resources/reference-designs/lpc54018-iot-solution-with-amazon-freertos:IoT-Solution-with-Amazon-FreeRTOS?tid=vanLPC-AWS-Module)\.

### Microchip Curiosity PIC32MZEF<a name="mch"></a>

The Curiosity PIC32MZEF development board from Microchip includes a PIC32MZEF MCU with a 200 MHz 32\-bit MIPS M\-class core with 2 MB of Flash and 512 KB of SRAM\. For users who need to use Ethernet, the [LAN8720A Ethernet PHY daughter board](https://www.microchip.com/wwwproducts/en/LAN8720A) can be connected to the Curiosity PIC32MZEF development board\. For more information about this platform, see [Microchip PIC32MZ2048EFM100](http://www.microchip.com/wwwproducts/en/PIC32MZ2048EFM100)\.
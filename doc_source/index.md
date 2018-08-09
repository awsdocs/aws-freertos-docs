# Amazon FreeRTOS User Guide

-----
*****Copyright &copy; 2018 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is Amazon FreeRTOS?](what-is-amazon-freertos.md)
   + [Development Workflow](development-workflow.md)
+ [Getting Started with Amazon FreeRTOS](freertos-getting-started.md)
   + [Prerequisites](freertos-prereqs.md)
   + [Getting Started with the Texas Instruments CC3220SF-LAUNCHXL](getting_started_ti.md)
   + [Getting Started with the STMicroelectronics STM32L4 Discovery Kit IoT Node](getting_started_st.md)
   + [Getting Started with the NXP LPC54018 IoT Module](getting_started_nxp.md)
   + [Getting Started with the Microchip Curiosity PIC32MZEF](getting_started_mch.md)
   + [Getting Started with the Espressif ESP32-DevKitC and the ESP-WROVER-KIT](getting_started_espressif.md)
   + [Getting Started with the FreeRTOS Windows Simulator](getting_started_windows.md)
+ [Amazon FreeRTOS Developer Guide](freertos-dev-guide.md)
   + [Amazon FreeRTOS Architecture](freertos-architecture.md)
   + [FreeRTOS Kernel Fundamentals](dev-guide-freertos-kernel.md)
   + [Amazon FreeRTOS Libraries](dev-guide-freertos-libraries.md)
      + [Cloud Connectivity](freertos-lib-cloud-connectivity.md)
      + [Greengrass Connectivity](freertos-lib-gg-connectivity.md)
      + [Amazon FreeRTOS Security](freertos-security.md)
         + [Secure Sockets](secure-sockets.md)
         + [Transport Layer Security](security-tls.md)
         + [Public Key Cryptography Standard #11](security-pkcs.md)
      + [FreeRTOS Wi-Fi Interface](freertos-wifi.md)
      + [OTA agent Library](ota-agent-library.md)
      + [Amazon FreeRTOS Device Defender Library](afr-device-defender-library.md)
   + [Amazon FreeRTOS Over-the-Air Updates](freertos-ota-dev.md)
      + [Over-the-Air Update Prerequisites](ota-prereqs.md)
      + [OTA Tutorial](dev-guide-ota-workflow.md)
         + [Installing the Initial Firmware](dg-ota-initial-firmware.md)
            + [Install the Initial Version of Firmware on the Texas Instruments CC3200SF-LAUNCHXL](burn-initial-firmware-ti.md)
            + [Install the Initial Version of Firmware on the Microchip Curiosity PIC32MZEF](burn-initial-firmware-microchip.md)
            + [Install the Initial Version of Firmware on a custom board](burn-initial-firmware-other.md)
         + [Update the Version of your Firmware](dg-ota-create-update.md)
         + [Creating an OTA Update with the AWS IoT Console](ota-console-workflow.md)
         + [Creating an OTA Update with the AWS CLI](ota-cli-workflow.md)
   + [Amazon FreeRTOS Console User Guide](freertos-ocw-ug.md)
   + [Downloading Amazon FreeRTOS from GitHub](freertos-mds-projects-github.md)
   + [Amazon FreeRTOS Qualification Program](freertos-qualification-program.md)
+ [Amazon FreeRTOS Demo Projects](freertos-next-steps.md)
   + [Navigating the Demo Applications](freertos-mds-projects-struct.md)
   + [Device Shadow Demo Application](shadow-demo.md)
   + [Greengrass Discovery Demo Application](gg-demo.md)
   + [OTA Demo Application](ota-demo.md)
+ [Amazon FreeRTOS Porting Guide](porting-guide.md)
   + [Bootloader](porting-bootloader.md)
   + [Logging](porting-logging.md)
   + [Connectivity](porting-connectivity.md)
   + [Security](porting-security.md)
   + [Using Custom Libraries with Amazon FreeRTOS](freertos-custom-libs.md)
   + [OTA Portable Abstraction Layer](porting-ota-pal.md)
# FreeRTOS Porting Guide

-----
*****Copyright &copy; 2020 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

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
+ [FreeRTOS Porting](porting-guide.md)
+ [Downloading FreeRTOS for Porting](porting-download.md)
+ [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)
   + [Configuring the FreeRTOS download](porting-set-up-directory.md)
   + [Setting up your FreeRTOS source code for testing](testing-set-up.md)
      + [Creating an IDE project](porting-create-project.md)
      + [Creating a CMake list file](porting-cmake-setup.md)
         + [Prerequisites](building-cmake-prereqs.md)
         + [Creating a list file for your platform from the CMakeLists.txt template](cmake-template.md)
         + [Building FreeRTOS with CMake](building-cmake.md)
+ [Porting the FreeRTOS Libraries](afr-porting.md)
   + [FreeRTOS porting flowchart](porting-chart.md)
   + [Implementing the configPRINT_STRING() macro](afr-porting-config.md)
   + [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)
   + [Porting the Wi-Fi library](afr-porting-wifi.md)
   + [Porting a TCP/IP stack](afr-porting-tcp.md)
      + [Porting FreeRTOS+TCP](porting-freertos-tcp.md)
      + [Porting lwIP](porting-lwip.md)
   + [Porting the Secure Sockets library](afr-porting-ss.md)
      + [Setting up an echo server](afr-echo-server.md)
   + [Porting the corePKCS11 library](afr-porting-pkcs.md)
   + [Porting the TLS library](afr-porting-tls.md)
      + [Connecting your device to AWS IoT](testing-connect-iot.md)
      + [Setting up certificates and keys for the TLS tests](tls-certkey-setup.md)
      + [Creating a BYOC (ECDSA)](afr-byoc.md)
         + [ca.config](porting-ca-config.md)
   + [Configuring the coreMQTT library for testing](afr-porting-mqtt.md)
   + [Configuring the HTTPS client library for testing](afr-porting-https.md)
   + [Porting the OTA library](afr-porting-ota.md)
   + [Porting the Bluetooth Low Energy library](afr-porting-ble.md)
   + [Perform Over the Air Updates using Bluetooth Low Energy](ota-updates-ble.md)
   + [Porting the common I/O libraries](freertos-porting-commonio.md)
+ [Migrating from Version 1.4.x to Version 201906.00 (and newer)](porting-migration.md)
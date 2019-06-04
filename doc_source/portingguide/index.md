# Amazon FreeRTOS Porting Guide

-----
*****Copyright &copy; 2019 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

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
+ [Porting Amazon FreeRTOS to Your Device](porting-guide.md)
+ [Downloading Amazon FreeRTOS for Porting](porting-download.md)
+ [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md)
   + [Configuring the Amazon FreeRTOS Download](porting-set-up-directory.md)
   + [Setting Up Your Amazon FreeRTOS Source Code for Testing](testing-set-up.md)
      + [Creating an IDE Project](porting-create-project.md)
+ [Porting the Amazon FreeRTOS Libraries](afr-porting.md)
   + [Amazon FreeRTOS Porting Flowchart](porting-chart.md)
   + [Implementing the configPRINT_STRING() macro](afr-porting-config.md)
   + [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)
   + [Porting the Wi-Fi Library](afr-porting-wifi.md)
   + [Porting a TCP/IP Stack](afr-porting-tcp.md)
      + [Porting FreeRTOS+TCP](porting-freertos-tcp.md)
      + [Porting lwIP](porting-lwip.md)
   + [Porting the Secure Sockets Library](afr-porting-ss.md)
      + [Setting Up an Echo Server](afr-echo-server.md)
         + [Setting Up the TLS Echo Server](tls-echo-server.md)
         + [Setting Up the Echo Server (Without TLS)](notls-echo-server.md)
   + [Porting the PKCS #11 Library](afr-porting-pkcs.md)
   + [Porting the TLS Library](afr-porting-tls.md)
      + [Connecting Your Device to AWS IoT](testing-connect-iot.md)
      + [Setting Up Certificates and Keys for the TLS Tests](tls-certkey-setup.md)
      + [Creating a BYOC (ECDSA)](afr-byoc.md)
         + [ca.config](porting-ca-config.md)
   + [Setting Up the MQTT Library for Testing](afr-porting-mqtt.md)
   + [Porting the OTA Library](afr-porting-ota.md)
      + [Porting the Bootloader Demo](afr-porting-bootloader.md)
   + [Porting the BLE Library](afr-porting-ble.md)
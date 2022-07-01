# FreeRTOS Porting Guide

-----
*****Copyright &copy; Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

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
+ [Setting up your workspace and project for porting](porting-set-up-project.md)
+ [Porting the FreeRTOS libraries](afr-porting.md)
   + [FreeRTOS porting flowchart](porting-chart.md)
   + [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)
   + [Implementing the library logging macros](afr-library-logging-macros.md)
   + [Porting a TCP/IP stack](afr-porting-tcp.md)
   + [Porting the corePKCS11 library](afr-porting-pkcs.md)
   + [Porting the Network Transport Interface](afr-porting-network-transport-interface.md)
   + [Configuring the coreMQTT library](afr-porting-mqtt.md)
   + [Configuring the coreHTTP library](afr-porting-corehttp.md)
   + [Porting the AWS IoT over-the-air (OTA) update library](afr-porting-ota.md)
   + [Porting the Cellular Interface library](freertos-porting-cellular.md)
+ [Migrating from version 1 to version 3 for OTA applications](porting-migration-ota.md)
+ [Migrating from version 1 to version 3 for OTA PAL port](porting-migration-ota-pal.md)
+ [Document history](doc-history.md)
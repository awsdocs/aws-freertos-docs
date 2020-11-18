# Porting the FreeRTOS Libraries<a name="afr-porting"></a>

Before you start porting, follow the instructions in [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.

To port FreeRTOS to your device, follow the instructions in the topics below\.

1. [Implementing the `configPRINT_STRING()` macro](afr-porting-config.md)

1. [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)

1. [Porting the Wi\-Fi library](afr-porting-wifi.md)
**Note**  
If your device does not support Wi\-Fi, you can use an ethernet connection to connect to the AWS Cloud instead\. A port of the FreeRTOS Wi\-Fi library is not necessarily required\.

1. [Porting a TCP/IP stack](afr-porting-tcp.md)

1. [Porting the Secure Sockets library](afr-porting-ss.md)

1. [Porting the corePKCS11 library](afr-porting-pkcs.md)

1. [Porting the TLS library](afr-porting-tls.md)

1. [Configuring the coreMQTT library for testing](afr-porting-mqtt.md)

1. [Configuring the HTTPS client library for testing](afr-porting-https.md)
**Note**  
Currently, a port of the FreeRTOS HTTPS library is not required for qualification\.

1. [Porting the OTA library](afr-porting-ota.md)
**Note**  
Currently, a port of the FreeRTOS OTA update library is not required for qualification\.

1. [Porting the Bluetooth Low Energy library](afr-porting-ble.md)
**Note**  
Currently, a port of the FreeRTOS Bluetooth Low Energy library is not required for qualification\.

1. [Perform Over the Air Updates using Bluetooth Low Energy](ota-updates-ble.md)
**Note**  
Currently AWS IoT Device Tester does not support qualification of Over the Air updates using Bluetooth Low Energy library\. A partner interested in this qualification should contact AWS through the APN \(AWS Partner Network\) team\.

1. [Porting the common I/O libraries](freertos-porting-commonio.md)
**Note**  
Currently, a port of the FreeRTOS common I/O library is not required for qualification\.

After you port FreeRTOS to your board, you can officially validate the ports for FreeRTOS qualification with AWS IoT Device Tester for FreeRTOS\. For more information about AWS IoT Device Tester for FreeRTOS, see [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide\. 

**Note**  
Before you validate your port of FreeRTOS using the AWS IoT Device Tester for FreeRTOS you must remove any logging messages that you might have inserted in your code for testing purposes, otherwise the validation may fail\.

For information about qualifying your device for FreeRTOS, see the [FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\. 
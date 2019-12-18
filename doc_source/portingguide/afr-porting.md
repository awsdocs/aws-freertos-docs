# Porting the Amazon FreeRTOS Libraries<a name="afr-porting"></a>

Before you start porting, follow the instructions in [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md)\.

To port Amazon FreeRTOS to your device, follow the instructions in the topics below\.

1. [Implementing the `configPRINT_STRING()` macro](afr-porting-config.md)

1. [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)

1. [Porting the Wi\-Fi Library](afr-porting-wifi.md)
**Note**  
If your device does not support Wi\-Fi, you can use an ethernet connection to connect to the AWS Cloud instead\. A port of the Amazon FreeRTOS Wi\-Fi library is not necessarily required\.

1. [Porting a TCP/IP Stack](afr-porting-tcp.md)

1. [Porting the Secure Sockets Library](afr-porting-ss.md)

1. [Porting the PKCS \#11 Library](afr-porting-pkcs.md)

1. [Porting the TLS Library](afr-porting-tls.md)

1. [Configuring the MQTT Library for Testing](afr-porting-mqtt.md)

1. [Configuring the HTTPS Client Library for Testing](afr-porting-https.md)
**Note**  
Currently, a port of the Amazon FreeRTOS HTTPS library is not required for qualification\.

1. [Porting the OTA Library](afr-porting-ota.md)
**Note**  
Currently, a port of the Amazon FreeRTOS OTA update library is not required for qualification\.

1. [Porting the Bluetooth Low Energy Library](afr-porting-ble.md)
**Note**  
Currently, a port of the Amazon FreeRTOS Bluetooth Low Energy library is not required for qualification\.

1. [Porting the Common I/O Libraries](afr-porting-commonio.md)
**Note**  
Currently, a port of the Amazon FreeRTOS common I/O library is not required for qualification\.

After you port Amazon FreeRTOS to your board, you can officially validate the ports for Amazon FreeRTOS qualification with AWS IoT Device Tester for Amazon FreeRTOS\. For more information about AWS IoT Device Tester for Amazon FreeRTOS, see [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide\. 

**Note**  
Before you validate your port of Amazon FreeRTOS using the AWS IoT Device Tester for Amazon FreeRTOS you must remove any logging messages that you might have inserted in your code for testing purposes, otherwise the validation may fail\.

For information about qualifying your device for Amazon FreeRTOS, see the [Amazon FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\. 
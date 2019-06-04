# Amazon FreeRTOS Qualification Checklist<a name="afq-checklist"></a>

Use following checklist to help you keep track of qualification items\.

□ Port Amazon FreeRTOS\.  
Make sure that you have ported and tested the following all of the libraries, according to the instructions in the [Amazon FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.    
□ Implement `configPRINT_STRING()` macro\.  
For instructions, see [Implementing the configPRINT\_STRING\(\) macro](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-config.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Configure FreeRTOS kernel for the target board\.  
For instructions, see [Configuring a FreeRTOS Kernel Port](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-kernel.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port Wi\-Fi library\.  
This port is not required for qualification if your board does not support Wi\-Fi\.
For instructions, see [Porting the Wi\-Fi Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-wifi.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port TCP/IP stack\.  
For instructions, see [Porting a TCP/IP Stack](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tcp.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port Secure Sockets library\.  
For instructions, see [Porting the Secure Sockets Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ss.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port PKCS \#11 library\.  
For instructions, see [Porting the PKCS \#11 Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-pkcs.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port TLS library\.  
For instructions, see [Porting the TLS Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tls.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Test MQTT library\.  
For instructions, see [Setting Up the MQTT Library for Testing](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-mqtt.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port OTA library\.  
This port is currently not required for qualification\.
For instructions, see [Porting the OTA Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html) in the *Amazon FreeRTOS Porting Guide*\.  
□ Port BLE library\.  
This port is currently not required for qualification\.
For instructions, see [Porting the BLE Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ble.html) in the *Amazon FreeRTOS Porting Guide*\.

□ Validate your Amazon FreeRTOS ports with AWS IoT Device Tester\.  
For more information, see [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *Amazon FreeRTOS User Guide*\.

□ Set up Hello World demo\.  
For instructions, see [Setting Up a Hello World Demo](afq-hw-demo.md)\.

□ Create a Getting Started Guide for your device\.  
For instructions, see [Creating a Getting Started with Amazon FreeRTOS Guide for Your Device](afq-gsg.md)\.

□ Create a CMake list file, and build the test and demo applications with the file\.  
For instructions, see [Creating a CMakeLists\.txt File for Your Platform](afq-cmake.md)\.

□ Provide hardware information for your device\.  
For instructions, see [Hardware Information for Amazon FreeRTOS Qualification](afq-hardware.md)\.

□ Create and place an appropriate open source license text file with your code\.  
For instructions, see [Providing an Open Source License for Your Code](afq-license.md)\.

□ Run the Amazon FreeRTOS qualification check script\.  
For instructions, see [Amazon FreeRTOS Qualification Check Script](afq-script.md)\.
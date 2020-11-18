# Porting a TCP/IP stack<a name="afr-porting-tcp"></a>

FreeRTOS provides a TCP/IP stack for boards that do not have on\-chip TCP/IP functionality\. If your platform offloads TCP/IP functionality to a separate network processor or module, you can skip this porting section and start [Porting the Secure Sockets library](afr-porting-ss.md)\.

FreeRTOS\+TCP is a native TCP/IP stack for the FreeRTOS kernel\. FreeRTOS\+TCP is maintained by the FreeRTOS engineering team and is the recommended TCP/IP stack to use with FreeRTOS\. For more information, see [Porting FreeRTOS\+TCP](porting-freertos-tcp.md)\.

The lightweight IP \(lwIP\) TCP/IP stack is an open source third\-party TCP/IP stack, ported to the FreeRTOS kernel\. The lwIP port layer currently supports lwIP version 2\.03\. For more information, see [Porting lwIP](porting-lwip.md)\.

**Note**  
These porting sections only provide instructions for porting to a platform's Ethernet or Wi\-Fi driver\. The tests only ensure that the Ethernet or Wi\-Fi driver can connect to a network\. You cannot test sending and receiving data across a network until you have ported the Secure Sockets library\.

A Secure Sockets library implementation already exists for the FreeRTOS\+TCP TCP/IP stack and the lwIP stack\. If you are using FreeRTOS\+TCP or lwIP, you do not need to port the Secure Sockets library\. After you finish porting the FreeRTOS\+TCP stack or the lwIP stack to your device, you can start [Porting the corePKCS11 library](afr-porting-pkcs.md)\. Even if you do not need to create a port for the Secure Sockets library, your platform still needs to pass the AWS IoT Device Tester tests for the Secure Sockets library for qualification\.
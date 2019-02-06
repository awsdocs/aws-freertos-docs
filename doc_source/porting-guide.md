# Porting Your Device<a name="porting-guide"></a>

This porting guide walks you through modifying the Amazon FreeRTOS software package to work on boards that are not Amazon FreeRTOS qualified\. Amazon FreeRTOS is designed to let you choose only those libraries required by your board or application\. The MQTT, Shadow, Defender, and Greengrass libraries are designed to be compatible with most devices as\-is, so there is no porting guide for these libraries\.

Before you can port the Amazon FreeRTOS libraries, you need to port the FreeRTOS kernel to your device\. For information about porting the FreeRTOS kernel, see [FreeRTOS Kernel Porting Guide](http://www.freertos.org/FreeRTOS-porting-guide.html)\.

For information about porting and testing Amazon FreeRTOS libraries for qualification, see the [Amazon FreeRTOS Qualification Developer Guide](https://github.com/aws/amazon-freertos/blob/master/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)\.

**Topics**
+ [Bootloader](porting-bootloader.md)
+ [Logging](porting-logging.md)
+ [Connectivity](porting-connectivity.md)
+ [Security](porting-security.md)
+ [Using Custom Libraries with Amazon FreeRTOS](freertos-custom-libs.md)
+ [OTA Portable Abstraction Layer](porting-ota-pal.md)
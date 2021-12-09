# Cellular Interface library<a name="cellular-interface"></a>

## Introduction<a name="freertos-cellular-interface-introduction"></a>

The Cellular Interface library exposes the capabilities of a few popular cellular modems through a uniform API\. Currently, this project contains libraries for these three cellular modems\. 
+ [Quectel BG96](https://www.quectel.com/product/lte-bg96-cat-m1-nb1-egprs/)
+ [ Sierra Wireless HL7802 ](https://www.sierrawireless.com/products-and-solutions/embedded-solutions/products/hl7802/)
+ [U\-Blox Sara\-R4](https://www.u-blox.com/en/product/sara-r4-series)

The current version of the Cellular Interface library encapsulates the TCP stack offered by those cellular modems\. They all implement the same uniform [ Cellular Interface library API](https://www.freertos.org/Documentation/api-ref/cellular/index.html)\. That API hides the complexity of AT commands, and exposes a socket\-like interface to C programmers\.

Even though applications can choose to use the Cellular Interface API directly, it is not designed for this purpose\. In a typical FreeRTOS system, applications use higher level libraries, such as the [coreMQTT](https://www.freertos.org/mqtt/index.html) library and the [coreHTTP](https://www.freertos.org/http/index.html) library, to communicate with other end points\. These higher level libraries use an abstract interface, the [Transport Interface](https://www.freertos.org/network-interface.html), to send and receive data\. A Transport Interface can be implemented on top of the Cellular Interface library; the  [Cellular Interface Demo](https://www.freertos.org/cellular-demo.html) uses such an implementation\.

Most cellular modems implement more or less of the AT commands defined by the [ 3GPP TS v27\.007](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=1515) standard\. This project provides an [implementation](https://github.com/FreeRTOS/FreeRTOS-Cellular-Interface/tree/main/source) of such standard AT commands in a [ reusable common component](https://freertos.org/Documentation/api-ref/cellular/cellular_porting_module_guide.html)\. The three Cellular Interface libraries in this project all take advantage of that common code\. The library for each modem only implements the vendor\-specific AT commands, then exposes the complete Cellular Interface library API\.

The common component that implements the 3GPP TS v27\.007 standard has been written in compliance with the following code quality criteria: 
+ GNU Complexity scores are not over 8
+ MISRA C:2012 coding standard\. Any deviations from the standard are documented in source code comments marked by "coverity"\.

## Dependencies and requirements<a name="freertos-cellular-interface-dependencies"></a>

There is no direct dependency for the Cellular Interface library\. However, Ethernet, Wi\-Fi and cellular cannot co\-exist in the FreeRTOS network stack\. Developers must choose one of the network interface to integrate with the [Secure Sockets library](https://docs.aws.amazon.com/freertos/latest/userguide/secure-sockets.html)\. 

## Porting<a name="freertos-cellular-interface-porting"></a>

For information about porting the Cellular Interface library to your platform, see [ Porting the Cellular Interface library](https://docs.aws.amazon.com/freertos/latest/portingguide/freertos-porting-cellular.html) in the *FreeRTOS Porting Guide*\. 

## Memory use<a name="freertos-cellular-interface-memory-use"></a>


****  

| Code Size of cellular interface library \(example generated with GCC for ARM Cortex\-M\) | File | With \-O1 Optimization | With \-Os Optimization | 
| --- | --- | --- | --- | 
| cellular\_3gpp\_api\.c | 6\.3K | 5\.7K | 
| cellular\_3gpp\_urc\_handler\.c | 0\.9K | 0\.8K | 
| cellular\_at\_core\.c | 1\.4K | 1\.2K | 
| cellular\_common\_api\.c | 0\.5K | 0\.5K | 
| cellular\_common\.c | 1\.6K | 1\.4K | 
| cellular\_pkthandler\.c | 1\.4K | 1\.2K | 
| cellular\_pktio\.c | 1\.8K | 1\.6K | 
| Total estimates | 13\.9K | 12\.4K | 

## Getting started<a name="freertos-cellular-interface-getting-started"></a>

### Download the source code<a name="freertos-cellular-interface-download-source"></a>

The source code can be downloaded as part of the FreeRTOS libraries or by itself\.

To clone the library from Github using HTTPS: 

```
git clone https://github.com/FreeRTOS/FreeRTOS-Cellular-Interface.git 
```

Using SSH:

```
git clone git@github.com:FreeRTOS/FreeRTOS-Cellular-Interface.git 
```

### Folder structure<a name="freertos-cellular-interface-folder-structure"></a>

At the root of this repository you will see these folders: 
+ `source` : reusable common code that implements the standard AT commands defined by 3GPP TS v27\.007
+ `modules` : vendor\-specific code that implements non\-3GPP AT commands for each cellular modem
+ `doc` : documentation
+ `test` : unit test and cbmc
+ `tools` : tools for Coverity static analysis and CMock

### Configure and build the Library<a name="freertos-cellular-interface-configure"></a>

The Cellular Interface library should be built as part of an application\. In order to do this, you must provide certain configurations\. The [ FreeRTOS\_Cellular\_Interface\_Windows\_Simulator](https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS-Plus/Demo/FreeRTOS_Cellular_Interface_Windows_Simulator) project provides an [ example](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/FreeRTOS_Cellular_Interface_Windows_Simulator/MQTT_Mutual_Auth_Demo_with_BG96/cellular_config.h) of how to configure the build\. More information can be found in the [Cellular API References](https://www.freertos.org/Documentation/api-ref/cellular/cellular_config.html)\. 

Please refer to the [Cellular Interface](https://www.freertos.org/cellular/index.html) page for more information\.

## Integrate the Cellular Interface library with MCU platforms<a name="freertos-cellular-interface-integrate"></a>

The Cellular Interface library runs on MCUs using an abstracted interface, the [ Comm Interface](https://github.com/FreeRTOS/FreeRTOS-Cellular-Interface/blob/main/source/interface/cellular_comm_interface.h), to communicate with cellular modems\. A Comm Interface must be implemented on the MCU platform as well\. The most common implementations of the Comm Interface communicate over UART hardware, but can be implemented over other physical interfaces, such as SPI, as well\. The documentation for the Comm Interface can be found in the [ Cellular Library API References](https://www.freertos.org/Documentation/api-ref/cellular/cellular_porting.html#cellular_porting_comm_if)\. The following example implementations of the Comm Interface are available:
+ [ FreeRTOS Windows simulator comm interface](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/FreeRTOS_Cellular_Interface_Windows_Simulator/Common/comm_if_windows.c)
+ [  FreeRTOS Common IO UART comm interface](https://github.com/aws/amazon-freertos/blob/main/libraries/abstractions/common_io/include/iot_uart.h)
+ [ STM32 L475 discovery board comm interface](https://github.com/aws/amazon-freertos/blob/feature/cellular/vendors/st/boards/stm32l475_discovery/ports/comm_if/comm_if_uart.c)
+ [ Sierra Sensor Hub board comm interface](https://github.com/aws/amazon-freertos/blob/feature/cellular/vendors/sierra/boards/sensorhub/ports/comm_if/comm_if_sierra.c)
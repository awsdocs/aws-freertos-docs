# Common I/O<a name="common-io"></a>

**Overview **

In general, device drivers are independent of the underlying operating system and are specific to a given hardware configuration\. A hardware abstraction layer \(HAL\) provides a common interface between drivers and higher\-level application code\. The HAL abstracts away the details of how a specific driver works and provides a uniform API to control such devices\. You can use the same APIs to access various device drivers across multiple microcontroller \(MCU\) based reference boards\.

FreeRTOS [common I/O](https://docs.aws.amazon.com/freertos/latest/lib-ref/common-io/index.html) acts as this hardware abstraction layer\. It provides a set of standard APIs for accessing common serial devices on supported reference boards\. These common APIs communicate and interact with these peripherals and enable your code to function across platforms\. Without common I/O, writing code to work with low level devices is silicon\-vendor specific\.

**Supported peripherals**
+ UART
+ SPI
+ I2C

**Supported features**
+ Synchronous read/write – The function doesn't return until the requested amount of data is transferred\. 
+ Asynchronous read/write – The function returns immediately and the data transfer happens asynchronously\. When the action completes, a registered user callback is invoked\.

**Peripheral specific**
+ I2C – Combine multiple operations into one transaction\. Used to do write then read actions in one transaction\. 
+ SPI – Transfer data between primary and secondary, which means the write and read happen simultaneously\.

**Porting**

For more information, see the [ FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.
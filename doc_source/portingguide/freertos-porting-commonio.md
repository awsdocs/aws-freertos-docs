# Porting the Common I/O Libraries<a name="freertos-porting-commonio"></a>

In general, device drivers are independent of the underlying operating system and are specific to a given hardware configuration\. A hardware abstraction layer \(HAL\) is a wrapper that provides common interfaces between drivers and higher\-level application code\. The HAL abstracts away the details of how a specific driver works and provides a uniform API to control similar devices\. In this way, you can use the same APIs to control various devices across multiple microcontroller \(MCU\) based reference boards\.

FreeRTOS common I/O acts as a hardware abstraction layer\. It provides a set of standard APIs for accessing common serial devices across supported reference boards\. These APIs communicate and interact with some common peripherals and enable your application code to function across platforms\. Without common I/O, the code that is required to work with low\-level devices is silicon vendor specific\.

**Supported Peripherals**
+ UART
+ SPI
+ I2C

**Supported Features**
+ Synchronous read/write – The function doesn't return until the requested amount of data has been transferred\. 
+ Asynchronous read/write – The function returns immediately and the data transfer happens asynchronously\. When function execution completes, a registered user callback is invoked\. 

**Peripheral Specific**
+ I2C – Combine multiple operations into one transaction, typically to do write then read operations in one transaction\.
+ SPI – Transfer data between primary and secondary, which means the write and read operations happen simultaneously\.

Porting

See the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.

**Topics**
+ [Prerequisites](#porting-commonio-prerequisites)
+ [Testing](#porting-commonio-testing)
+ [Porting the I2C Library](#porting-commonio-i2c)
+ [Porting the UART Library](#porting-commonio-uart)
+ [Porting the SPI Library](#porting-commonio-spi)

## Prerequisites<a name="porting-commonio-prerequisites"></a>

To port the common I/O Libraries, you need the following:
+ An IDE project or `CMakeLists.txt` list file that includes the vendor\-supplied I/O drivers\.
+ A validated configuration of the FreeRTOS kernel\.

## Testing<a name="porting-commonio-testing"></a>

First, either set up an IDE project or configure CMake\.

**Set Up Your Local Testing Environment**

No changes are required in the test file `<freertos>/libraries/abstractions/common_io/test/test_iot_peripheral.c`\. 

Device\-specific code is in the following files 
+ `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h`
+ `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c`

**To set up your local testing environment**

1. Create a test configuration header file named `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h`\. For its content, refer to the "`Test Setup`" section for each peripheral\.

1. Create a test setup file named `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c`\. For its content, refer to the "Test Setup" section for each peripheral\.

1. To enable the common I/O tests, open the file `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/aws_test_runner_config.h`\.****\.

1. Set `testrunnerFULL_COMMON_IO_ENABLED` to **1**

**Set Up the IDE Test Project**

If you're using an IDE for porting and testing, you must add the source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their current on\-disk location\. Don't create duplicate copies of the source files\.

**To set up the common I/O libraries in IDE project**

1. Add all implementation source files `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_<peripheral>.c` to your `aws_tests` IDE project \(one file per peripheral\)\.

1. Add all test source files `<freertos>/libraries/abstractions/common_io/test/test_iot_<peripheral>.c` to your `aws_tests` IDE project \(one file per peripheral\)\.

1. Add the test configuration file `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h` to your `aws_tests` IDE project \(just this one file for all peripherals\)\.

1. Add the test setup file `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c` to your `aws_tests` IDE project \(just this one file for all peripherals\)\.

**Configure the CMakeLists\.txt File**

If you're using CMake to build your test project, you must define a portable layer target for the library in your CMake list file\.

The `CMakeLists.txt` template list file at `<freertos>/vendors/<vendor>/boards/<board>/CMakeLists.txt` includes examples of portable layer target definitions\. Uncomment the definition of each library that you're porting, and modify it to fit your platform\.

**Example**  
The following is portable layer target definition for the common I/O library\.  

```
# Common I/O
afr_mcu_port(common_io)
target_sources(
    AFR::common_io::mcu_port
    INTERFACE 
        # peripheral implementations
        <freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_<peripheral_1>.c
        <freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_<peripheral_2>.c
        <freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_<peripheral_3>.c
        ...
        # test configuration and pre-steps
        <freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c
)

# -------------------------------------------------------------------------------------------------
# FreeRTOS demos and tests
# -------------------------------------------------------------------------------------------------

...

if(AFR_IS_TESTING)
    set(exe_target aws_tests)
else()
    set(exe_target aws_demos)
endif()

...

# link common io library along with others
target_link_libraries(
    ${exe_target}
    PRIVATE
        AFR::wifi
        AFR::utils
        AFR::common_io
)
```

**Run the Tests**

**To execute the common I/O tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.

## Porting the I2C Library<a name="porting-commonio-i2c"></a>

I2C library interfaces with vendor\-supplied I2C drivers\. If the device doesn't have an I2C peripheral, you can skip porting I2C interfaces\. The I2C library can only use the I2C peripheral that is on the device as the primary\. 

**Prerequisites**

To port the I2C library, you need an I2C secondary device\. It can be one of the following:
+ An onboard I2C sensor\.
+ An external device, such as a Raspberry PI\.

**Porting**

Use the vendor\-supplied I2C driver library to implement all the functions in `<freertos>/libraries/abstractions/common_io/include/iot_i2c.h`\. The header file provides the required API behavior information\. An implementation source file should be created and named `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_i2c.c`\.

If any of the I2C features are not supported on a target device, make the corresponding functions return `IOT_I2C_FUNCTION_NOT_SUPPORTED`\. For the list of functions that can return `IOT_I2C_FUNCTION_NOT_SUPPORTED`, see the API as documented in `<freertos>/libraries/abstractions/common_io/include/iot_i2c.h`\. 

*Anonymous Handle "struct IotI2CDescriptor"*

This usually encapsulates the driver's handle and variety of states\. See the following example\.

```
/* Suppose the data type of driver handle for I2C is Driver_I2C_Handle */
struct IotI2CDescriptor
{
    Driver_I2C_Handle xHandle;     /* Driver Handle. */
    IotI2CConfig_t xConfig;        /* Bus Configuration. */
    IotI2CCallback_t xCallback;    /* Callback function. */
    void * pvUserContext;          /* User context passed in callback. */
    uint16_t usSlaveAddr;          /* Slave Address. */
    uint16_t usTransmittedTxBytes; /* Number of Transmitted Bytes */
    uint16_t usReceivedRxBytes;    /* Number of Received Bytes */
    SemaphoreHandle_t xSemphr;     /* Optional, useful when there is a synchronization situation. */
    /* State: if already opened. */
    /* State: if send no stop. */
};
```

### Test Setup<a name="porting-commonio-i2c-test-setup"></a>

**Hardware Setup**

If you're using an onboard sensor as a slave device, you can skip this step\.

If you use an external device, you need to wire the SDA \(data\) lines and SCL \(clock\) lines of the two devices\.

You can find the I2C test file in the following directory: `<freertos>/libraries/abstractions/common_io/test/test_iot_i2c.c` 

**To test the set up configurations**

1. Add the I2C configurations to `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h`\.   
`IOT_TEST_COMMON_IO_I2C_SUPPORTED`  
If this device has I2C peripheral, set to **1**\. Otherwise, set it to **0**\.  
`IOT_TEST_COMMON_IO_I2C_SUPPORTED_SEND_NO_STOP`  
If the I2C doesn't explicitly support sending stop condition, set to **1**\. Otherwise, set to **0**\.  
`IOT_TEST_COMMON_IO_I2C_SUPPORTED_CANCEL`  
If the I2C supports cancelling the asynchronous transaction with interrupt or DMA, set to **1**\. Otherwise, set to **0**\.  
`I2C_TEST_SET`  
Specify the number of I2C instances to test\.

1. Define test data in the `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h` file\.   
`i2cTestInstanceIdx`  
The I2C instance IDs\.  
`i2cTestInstanceNum`  
The total number of I2C instances\.  
`i2cTestSlaveAddr`  
Device address\.  
`i2cTestDeviceRegister`  
Register address on the test device\.  
`i2cTestWriteVal`  
A byte value to be written to the test device\.  
`gIotI2cHandle`  
Not used\. Define it as an array of null to compile\.  
**Example**  

   ```
   /* I2C includes */
   #include "iot_i2c.h"
   
   #define IOT_TEST_COMMON_IO_I2C_SUPPORTED                     1
   
   #if ( IOT_TEST_COMMON_IO_I2C_SUPPORTED == 1 )
       #define IOT_TEST_COMMON_IO_I2C_SUPPORTED_SEND_NO_STOP    1
       #define IOT_TEST_COMMON_IO_I2C_SUPPORTED_CANCEL          1
   #endif
   
   #define I2C_TEST_SET                                         1
   
   /* Slave address. */
   const uint8_t i2cTestSlaveAddr[ I2C_TEST_SET ] = { 0xD4 };
   /* Register address. */
   const uint8_t i2cTestDeviceRegister[ I2C_TEST_SET ] = { 0x73 };
   /* A value that is written to slave device during test. */
   const uint8_t i2cTestWriteVal[ I2C_TEST_SET ] = { 0b01101010 };
   /* I2C instance ID. */
   const uint8_t i2cTestInstanceIdx[ I2C_TEST_SET ] = { 1 };
   /* Total number of I2C instances. */
   const uint8_t i2cTestInstanceNum[ I2C_TEST_SET ] = { 3 };
   
   /* Unused, but this needs to be defined. */
   IotI2CHandle_t gIotI2cHandle[ 4 ] = { NULL, NULL, NULL, NULL };
   ```

1. Add I2C test setup code to the `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c` file\.

   ```
   #include "test_iot_internal.h"
   
   /* These global variables are defined in test_iot_i2c.c. */
   extern uint8_t uctestIotI2CSlaveAddr;
   extern uint8_t xtestIotI2CDeviceRegister;
   extern uint8_t uctestIotI2CWriteVal;
   extern uint8_t uctestIotI2CInstanceIdx;
   extern uint8_t uctestIotI2CInstanceNum;
   
   void SET_TEST_IOT_I2C_CONFIG(int testSet)
   {
       uctestIotI2CSlaveAddr = i2cTestSlaveAddr[testSet];
       xtestIotI2CDeviceRegister = i2cTestDeviceRegister[testSet];
       uctestIotI2CWriteVal = i2cTestWriteVal[testSet];
       uctestIotI2CInstanceIdx = i2cTestInstanceIdx[testSet];
       uctestIotI2CInstanceNum = i2cTestInstanceNum[testSet];
   }
   ```

## Porting the UART Library<a name="porting-commonio-uart"></a>

The UART library interfaces with vendor\-supplied UART drivers\. If the device doesn't have any UART peripherals, you can skip porting the UART interface\.

**Prerequisites**
+ Use a jump wire to connect the RX and TX of the UART for loopback testing\.

**Porting**

Use the vendor\-supplied UART driver library to implement all the functions in `<freertos>/libraries/abstractions/common_io/include/iot_uart.h`\. The header file provides information about the required API behavior\. An implementation source file should be created and named `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_uart.c`\. 

If the target device doesn't support any UART features, make the corresponding functions return `IOT_UART_FUNCTION_NOT_SUPPORTED`\. For the list of functions that can return `IOT_UART_FUNCTION_NOT_SUPPORTED`, refer to the API as documented in `<freertos>/libraries/abstractions/common_io/include/iot_uart.h`\. 

*Anonymous Handle "struct IotUARTDescriptor"*

This usually encapsulates driver's handle and variety of states\. See the following example\.

```
/* Suppose the data type of the driver handle for UART is UART_Handle */
struct IotUARTDescriptor
{
    IotUARTCallback_t xUartCallback; /* Application Specified callback. */
    UART_Handle * pxUartContext;     /* UART handle to be passed to driver functions. */
    void * pvUserCallbackContext;    /
    uint8_t sOpened;
};
```

### Test Setup<a name="porting-commonio-uart-test"></a>

**Hardware Setup**

On the UART port to test, connect the TX and RX for loopback by using a jump wire\.

You can find the UART test file in the following directory: `<freertos>/libraries/abstractions/common_io/test/test_iot_uart.c` 

**To test the setup configurations**

1. Add the UART configuration to the `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h` file\.  
`IOT_TEST_COMMON_IO_UART_SUPPORTED`  
If this device has UART peripheral, set to **1**\. Otherwise, set **0**\.  
`UART_TEST_SET`  
The number of UART instances to test\.

1. Define test data in the `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h` file\.   
`uartTestPort`  
The UART instance IDs\.  
`uartIotUartFlowControl`  
The UART flow control configuration\.  
`uartIotUartParity`  
The UART parity bit configuration\.  
`uartIotUartWordLength`  
The UART word length configuration\.  
`uartIotUartStopBits`  
The UART stop bit configuration\.  
**Example**  

   ```
   /* UART */
   #define UART_TEST_SET    1
   const uint8_t uartTestPort[ UART_TEST_SET ] = { 1 };
   const uint32_t uartIotUartFlowControl[ UART_TEST_SET ] = { UART_FLOW_CONTROL };
   const uint32_t uartIotUartParity[ UART_TEST_SET ] = { UART_PARITY };
   const uint32_t uartIotUartWordLength[ UART_TEST_SET ] = { UART_WORD_LENGTH };
   const uint32_t uartIotUartStopBits[ UART_TEST_SET ] = { UART_STOP_BITS };
   ```

1. Add the UART test setup code to the `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c` file\. 

   ```
   #include "test_iot_internal.h"
   
   /* UART */
   extern uint8_t ustestIotUartPort;
   extern uint32_t ultestIotUartFlowControl;
   extern uint32_t ultestIotUartParity;
   extern uint32_t ultestIotUartWordLength;
   extern uint32_t ultestIotUartStopBits;
   
   void SET_TEST_IOT_UART_CONFIG( int testSet )
   {
       ustestIotUartPort = uartTestPort[ testSet ];
       ultestIotUartFlowControl = uartIotUartFlowControl[ testSet ];
       ultestIotUartParity = uartIotUartParity[ testSet ];
       ultestIotUartWordLength = uartIotUartWordLength[ testSet ];
       ultestIotUartStopBits = uartIotUartStopBits[ testSet ];
   }
   ```

## Porting the SPI Library<a name="porting-commonio-spi"></a>

The SPI library interfaces with vendor\-supplied SPI drivers\. If the device doesn't have an SPI peripheral, you can skip porting the SPI interfaces\. The SPI library can only use the SPI peripheral on the device as master\.

**Porting**

Use the vendor\-supplied SPI driver library to implement all the functions in `<freertos>/libraries/abstractions/common_io/include/iot_spi.h`\. The header file provides the required API behavior information\. The implementation source file should be created as `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/iot_spi.c`\. 

It's possible that a target device doesn't support some SPI features\. In that case, make the corresponding functions return `IOT_SPI_FUNCTION_NOT_SUPPORTED`\. For the list of functions that can return `IOT_SPI_FUNCTION_NOT_SUPPORTED`, refer to the API as documented in `<freertos>/libraries/abstractions/common_io/include/iot_spi.h`\. 

*Anonymous Handle "struct IotSPIDescriptor"*

This usually encapsulates the driver's handle and variety of states\. See the following example\.

```
/* Suppose the data type of driver handle for SPI is Driver_SPI_Handle */
struct IotSPIDescriptor
{
    Driver_SPI_Handle xHandle;     /* Driver Handle. */
    IotSPIConfig_t xConfig;        /* Bus Configuration. */
    IotSPICallback_t xCallback;    /* Callback function. */
    void * pvUserContext;          /* User context passed in callback. */
    /* State: if already opened. */
};
```

### Test Setup<a name="porting-commonio-spi-test"></a>

You can find the SPI test file in the following directory: `<freertos>/libraries/abstractions/common_io/test/test_iot_spi.c` 

**To test the setup configurations**

1. Add the SPI configurations to the `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h` file\.   
`IOT_TEST_COMMON_IO_SPI_SUPPORTED`  
If the device has an SPI peripheral, set this to **1**\. Otherwise, set to **0**\.  
`SPI_TEST_SET`  
The number of SPI instances to test\.

1. Define test data in the `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/test_iot_config.h` file\.   
`spiTestPort`  
The SPI instance IDs\.  
`spiIotMode`  
The SPI mode\.  
`spiIotSpitBitOrder`  
The SPI bit order\.  
`spiIotFrequency`  
The SPI frequency\.  
`spiIotDummyValue`  
The dummy value\.  
**Example**  

   ```
   /* SPI includes */
   #include "iot_spi.h"
   
   #define IOT_TEST_COMMON_IO_SPI_SUPPORTED                     1
   #define I2C_TEST_SET                                         1
   
   const uint8_t spiTestPort[ SPI_TEST_SET ] = { 1 };
   const uint32_t spiIotMode[ SPI_TEST_SET ] = { eSPIMode0 };
   const uint32_t spiIotSpitBitOrder[ SPI_TEST_SET ] = { eSPIMSBFirst };
   const uint32_t spiIotFrequency[ SPI_TEST_SET ] = { 500000U };
   const uint32_t spiIotDummyValue[ SPI_TEST_SET ] = { 0 };
   ```

1. Add SPI test setup code in the `<freertos>/vendors/<vendor>/boards/<board>/ports/common_io/test_iot_internal.c` file\. 

   ```
   #include "test_iot_internal.h"
   
   /* SPI */
   extern uint8_t ultestIotSpiInstance;
   extern IotSPIMode_t xtestIotSPIDefaultConfigMode;
   extern IotSPIBitOrder_t xtestIotSPIDefaultconfigBitOrder;
   extern uint32_t ultestIotSPIFrequency;
   extern uint32_t ultestIotSPIDummyValue;
   
   void SET_TEST_IOT_SPI_CONFIG(int testSet)
   {
       ultestIotSpiInstance = spiTestPort[ testSet ] ;
       xtestIotSPIDefaultConfigMode = spiIotMode[ testSet ];
       xtestIotSPIDefaultconfigBitOrder = spiIotSpitBitOrder[ testSet ];
       ultestIotSPIFrequency = spiIotFrequency[ testSet ];
       ultestIotSPIDummyValue = spiIotDummyValue[ testSet ];
   }
   ```
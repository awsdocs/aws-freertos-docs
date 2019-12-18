# Prerequisites for OTA Updates using HTTP<a name="ota-http-freertos"></a>

This section describes the general requirements for using HTTP to perform over\-the\-air \(OTA\) updates\. Amazon FreeRTOS OTA can use the HTTP or MQTT protocol to transfer firmware update images from Amazon S3 to devices\. If you can specify both protocols when you create an OTA update in Amazon FreeRTOS\. Each device will determine the protocol used to transfer the image\. 

**Note**  
Device firmware must include the necessary Amazon FreeRTOS libraries \(for example, MQTT, HTTP, OTA agent\)\. Amazon FreeRTOS version 201912\.00 or later is required\.
Although the HTTP protocol might be used to transfer the firmware image, the MQTT library is still required because other interactions with AWS IoT Core use the MQTT library, including sending or receiving job execution notifications, job documents, and execution status updates\. 
When you specify both MQTT and HTTP protocols for the OTA update job, the setup of the OTA agent software on each individual device determines the protocol used to transfer the firmware image\. To change the OTA agent from the default MQTT protocol method to the HTTP protocol, you can modify the header files used to compile the Amazon FreeRTOS source code for the device\.

## Configurations<a name="ota-http-freertos-config"></a>

By default, see the following configuration of the OTA protocols in the `\vendors\boards\board\aws_demos\config_files\aws_ota_agent_config.h` file\.

```
/**
 * @brief The protocol selected for OTA control operations.
 * This configuration parameter sets the default protocol for all the OTA control
 * operations like requesting an OTA job, updating the job status, and so on.
 *
 * Note - Only MQTT is supported at this time for control operations.
 */
#define configENABLED_CONTROL_PROTOCOL       ( OTA_CONTROL_OVER_MQTT )
/**
 * @brief The protocol selected for OTA data operations.
 * This configuration parameter sets the protocols selected for the data operations
 * like requesting file blocks from the service.
 *
 * Note - Both MQTT and HTTP are supported for data transfer. This configuration parameter
 * can be set to the following -
 * Enable data over MQTT - ( OTA_DATA_OVER_MQTT )
 * Enable data over HTTP - ( OTA_DATA_OVER_HTTP)
 * Enable data over both MQTT & HTTP ( OTA_DATA_OVER_MQTT | OTA_DATA_OVER_HTTP )
 */
#define configENABLED_DATA_PROTOCOLS         ( OTA_DATA_OVER_MQTT )
 /**
  * @brief The preferred protocol selected for OTA data operations.
  *
  * Primary data protocol will be the protocol used for downloading files if more than
  * one protocol is selected while creating OTA job. Default primary data protocol is MQTT
  * and the following update here switches to HTTP as primary.
  *
  * Note - use OTA_DATA_OVER_HTTP for HTTP as primary data protocol.
  */
#define configOTA_PRIMARY_DATA_PROTOCOL     ( OTA_DATA_OVER_MQTT )
```

**To enable OTA data transfer over HTTP**

1. Change `configENABLED_DATA_PROTOCOLS` to `OTA_DATA_OVER_HTTP`\.

1. When the OTA updates, you can specify both protocols so that either MQTT or HTTP protocol can be used\., You can set the primary protocol used by the device to HTTP by changing `configOTA_PRIMARY_DATA_PROTOCOL` to `OTA_DATA_OVER_HTTP`\. 

**Note**  
HTTP is only supported for OTA data operations\. For control operations, you must use MQTT\.

## Device specific configurations<a name="ota-http-freertos-dev-config"></a>

**ESP32**  
Due to a limited amount of RAM, you must turn off BLE when you enable HTTP as an OTA data protocol\. In the `vendors/espressif/boards/esp32/aws_demos/config_files/aws_iot_network_config.h` file, change `configENABLED_NETWORKS` to `AWSIOT_NETWORK_TYPE_WIFI` only\.  

```
/**
     * @brief Configuration flag which is used to enable one or more network interfaces for a board.
     *
     * The configuration can be changed any time to keep one or more network enabled or disabled.
     * More than one network interfaces can be enabled by using 'OR' operation with flags for
     * each network types supported. Flags for all supported network types can be found
     * in "aws_iot_network.h"
     *
     */
    #define configENABLED_NETWORKS      ( AWSIOT_NETWORK_TYPE_WIFI )
```

## Memory Usage<a name="ota-http-freertos-memory"></a>

When MQTT is used for data transfer, no additional heap memory is required for the MQTT connection because it's shared between control and data operations\. However, enabling data over HTTP requires additional heap memory\. The following is the heap memory usage data for all supported platforms, calculated using the Amazon FreeRTOS `xPortGetFreeHeapSize` API\. You must make sure there is enough RAM to use the OTA library\.

****Texas Instruments CC3220SF\-LAUNCHXL****  
Control operations \(MQTT\): 12 KB  
Data operations \(HTTP\): 10 KB  
 TI uses significantly less RAM because it does SSL on hardware, so it doesn't use the mbedtls library\.

****Microchip Curiosity PIC32MZEF****  
Control operations \(MQTT\): 65 KB  
Data operations \(HTTP\): 43 KB

****Espressif ESP32****  
Control operations \(MQTT\): 65 KB  
Data operations \(HTTP\): 45 KB  
BLE on ESP32 takes about 87 KB RAM\. There's not enough RAM to enable all of them, which is mentioned in the device specific configurations above\.

****Windows simulator****  
Control operations \(MQTT\): 82 KB   
Data operations \(HTTP\): 63 KB 

****Nordic nrf52840\-dk****  
HTTP is not supported\.
# AWS IoT Over the air \(OTA\) library<a name="ota-update-library"></a>

## Introduction<a name="ota-update-library-intro"></a>

The [AWS IoT Over\-the\-air \(OTA\) update library](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/index.html) enables you to manage the notification, download, and verification of firmware updates for FreeRTOS devices using HTTP or MQTT as the protocol\. By using the OTA Agent library, you can logically separate firmware updates and the application running on your devices\. The OTA Agent can share a network connection with the application\. By sharing a network connection, you can potentially save a significant amount of RAM\. In addition, the OTA Agent library lets you define application\-specific logic for testing, committing, or rolling back a firmware update\.

The Internet of Things \(IoT\) extends internet connectivity to embedded devices that were traditionally not connected\. These devices can be programmed to communicate usable data over the internet, and can be remotely monitored and controlled\. With advances in technology, these traditional embedded devices are getting internet capabilities in consumer, industrial, and enterprise spaces at a fast pace\.

IoT devices are typically deployed in large quantities and often in places that are difficult or impractical for a human operator to access\. Imagine a scenario where a security vulnerability that can expose data is discovered\. In such scenarios, it is important to update the affected devices with security fixes quickly and reliably\. Without the ability to perform OTA updates, it can also be difficult to update devices that are geographically dispersed\. Having a technician update these devices will be costly, time consuming, and often times impractical\. The time required to update these devices leaves them exposed to security vulnerabilities for a longer period\. Recalling these devices for updating will also be costly and may cause significant disruption to consumers due to downtime\.

Over the Air \(OTA\) Updates make it possible to update device firmware without an expensive recall or technician visit\. This method adds the following benefits:
+ **Security** \- The ability to quickly respond to security vulnerabilities and software bugs that are discovered after the devices are deployed in the field\.
+ **Innovation** \- Products can be updated frequently as new features are developed, driving the innovation cycle\. The updates can take effect quickly with minimum downtime compared to traditional update methods\.
+ **Cost** \- OTA updates can reduce maintenance costs significantly compared to methods traditionally used to update these devices\.

Providing the OTA functionality requires the following design considerations:
+ **Secure Communication** \- Updates must use encrypted communication channels to prevent the downloads from being tampered with during transit\.
+ **Recovery** \- Updates can fail due to things like intermittent network connectivity or receiving an invalid update\. In these scenarios, the device needs to be able to return to a stable state and avoid becoming bricked\.
+ **Author Verification** \- Updates must be verified to be from a trusted source, along with other validations like checking the version and compatibility\.

For more information about setting up OTA updates with FreeRTOS, see [FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)\.

### AWS IoT Over the air \(OTA\) library<a name="ota-update-library-intro-detail"></a>

The AWS IoT OTA library enables you to manage notifications of a newly available updates, download them, and perform cryptographic verification of firmware updates\. Using the over\-the\-air \(OTA\) client library, you can logically separate the firmware update mechanisms from the application running on your device\. The over\-the\-air \(OTA\) client library can share a network connection with the application, saving memory in resource\-constrained devices\. In addition, the over\-the\-air \(OTA\) client library lets you define application\-specific logic for testing, committing, or rolling back a firmware update\. The library supports different application protocols like Message Queuing Telemetry Transport \(MQTT\) and Hypertext Transfer Protocol \(HTTP\) and provides various configuration options you can fine tune for your network type and conditions\.

This library's APIs provide these major functions:
+ Register for notifications or poll for new update requests that are available\.
+ Receive, parse and validate the update request\.
+ Download and verify the file according to the information in the update request\.
+ Run a self\-test before activating the received update to ensure the functional validity of the update\.
+ Update the status of the device\.

This library uses AWS services to manage various cloud related functions such as sending firmware updates, monitoring large numbers of devices across multiple regions, reducing the blast radius of faulty deployments, and verifying the security of updates\. This library can be used with any MQTT or HTTP library\. 

The demos for this library demonstrate complete over\-the\-air updates using the coreMQTT Library and AWS Services on a FreeRTOS device\.

## Features<a name="freertos-ota-features"></a>

Here is the complete OTA Agent interface:

**`[ OTA\_Init](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ae5cc1dcc13fbcb32230f552f48b24f03)`**  
Initializes the OTA engine by starting OTA Agent \("OTA Task"\) in the system\. Only one OTA Agent may exist\.

**`[ OTA\_Shutdown](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ac779291eb93f4e0e6459816e60e13b09)`**  
Signal to the OTA Agent to shut down\. The OTA agent will optionally unsubscribe from all MQTT job notification topics, stop in progress OTA jobs, if any, and clear all resources\.

**`[ OTA\_GetState](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a6db3f9cb417cb135cb0e68f5b5f2b11f)`**  
Gets the current state of the OTA Agent\.

**`[ OTA\_ActivateNewImage](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a5169ba09148e7f5668a90e776e712f8b)`**  
Activates the newest microcontroller firmware image received through OTA\. \(The detailed job status should now be self\-test\.\)

**`[ OTA\_SetImageState](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ab68cdf65934474e1f3d2cd1046314bea)`**  
Sets the validation state of the currently running microcontroller firmware image \(testing, accepted or rejected\)\.

**`[ OTA\_GetImageState](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a9c5b25f9a7eff3ded8cdf088c2011742)`**  
Gets the state of the currently running microcontroller firmware image \(testing, accepted or rejected\)\.

**`[ OTA\_CheckForUpdate](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a1178e8009eb05e6f55f6506b625c9fc2)`**  
Requests the next available OTA update from the OTA Update service\.

**`[ OTA\_Suspend](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a65b61ae5dd477e8b2e6c88ea0473c62b)`**  
Suspend all OTA Agent operations\.

**`[ OTA\_Resume](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ae9d40388ac87e4ac93288de37c98a138)`**  
Resume OTA Agent operations\.

**`[ OTA\_SignalEvent](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a2564144f284db077b8947ba58a6a72bb)`**  
Signal an event to the OTA Agent task\.

**`[ OTA\_EventProcessingTask](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ab3a0cfdc8694a606a1d736b2f54fb113)`**  
OTA agent event processing loop\.

**`[ OTA\_GetStatistics](https://docs.aws.amazon.com/freertos/latest/lib-ref/embedded-csdk/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a63182243ef3c18d5f36cd427b83a1a22)`**  
Get the statistics of OTA message packets which includes the number of packets received, queued, processed and dropped\.

**`[ OTA\_Err\_strerror](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a6f72911b8fe80f27bce42c3a36dca4b3)`**  
Error code to string conversion for OTA errors\.

**`[ OTA\_JobParse\_strerror ](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#a1d42efa1af7fa0ed92060a3b7e869648)`**  
Convert an OTA Job Parsing error code to a string\.

**`[ OTA\_PalStatus\_strerror](https://docs.aws.amazon.com/freertos/latest/lib-ref/embedded-csdk/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ad9d9a19df9fdb8977762a45fe2848ba7)`**  
Status code to string conversion for OTA PAL status\.

**`[ OTA\_OsStatus\_strerror](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_8c.html#ab5997df93a33e5f1260e7a16655a5cca)`**  
Status code to string conversion for OTA OS status\.

## API reference<a name="freertos-ota-api"></a>

For more information, see the [AWS IoT Over\-the\-air Update: Functions](https://docs.aws.amazon.com/freertos/latest/lib-ref/embedded-csdk/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_functions.html)\.

## Example usage<a name="freertos-ota-example"></a>

A typical OTA\-capable device application using the MQTT protocol drives the OTA Agent by using the following sequence of API calls\.

1. Connect to the AWS IoT coreMQTT Agent\. For more information, see [coreMQTT Agent library](coremqtt-agent.md)\.

1. Initialize the OTA Agent by calling `OTA_Init`, including the buffers, the required ota interfaces, the thing name and the application callback\. The callback implements application\-specific logic that executes after completing an OTA update job\. 

1. When the OTA update is complete, FreeRTOS calls the job completion callback with one of the following events: `accepted`, `rejected`, or `self test`\.

1. If the new firmware image has been rejected \(for example, due to a validation error\), the application can typically ignore the notification and wait for the next update\.

1. If the update is valid and has been marked as accepted, call `OTA_ActivateNewImage` to reset the device and boot the new firmware image\.

## Porting<a name="freertos-ota-porting"></a>

For information about porting OTA functionality to your platform, see [Porting the OTA Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html) in the FreeRTOS Porting Guide\.

## Memory use<a name="ota-update-library-memory"></a>


****  

| Code Size of AWS IoT OTA \(example generated with GCC for ARM Cortex\-M\) | File | With \-O1 Optimization | With \-Os Optimization | 
| --- | --- | --- | --- | 
| ota\.c | 8\.0K | 7\.2K | 
| ota\_interface\.c | 0\.1K | 0\.1K | 
| ota\_base64\.c | 0\.6K | 0\.6K | 
| ota\_mqtt\.c | 2\.3K | 2\.2K | 
| ota\_cbor\.c | 0\.8K | 0\.6K | 
| ota\_http\.c | 0\.3K | 0\.3K | 
| Total estimates | 12\.1K | 11\.0K | 
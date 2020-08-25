# OTA Agent library<a name="ota-agent-library"></a>

## Overview<a name="freertos-ota-overview"></a>

The [Over\-The\-Air \(OTA\) Agent](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/ota/index.html) enables you to manage the notification, download, and verification of firmware updates for FreeRTOS devices using HTTP or MQTT as the protocol\. By using the OTA Agent library, you can logically separate firmware updates and the application running on your devices\. The OTA Agent can share a network connection with the application\. By sharing a network connection, you can potentially save a significant amount of RAM\. In addition, the OTA Agent library lets you define application\-specific logic for testing, committing, or rolling back a firmware update\.

For more information about setting up OTA updates with FreeRTOS, see [FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)\.

## Features<a name="freertos-ota-features"></a>

Here is the complete OTA Agent interface:

`OTA_AgentInit`  
Initializes the OTA Agent\. The caller provides messaging protocol context, an optional callback, and a timeout\.

`OTA_AgentShutdown`  
Cleans up resources after using the OTA Agent\.

`OTA_GetAgentState`  
Gets the current state of the OTA Agent\.

`OTA_ActivateNewImage`  
Activates the newest microcontroller firmware image received through OTA\. \(The detailed job status should now be self\-test\.\)

`OTA_SetImageState`  
Sets the validation state of the currently running microcontroller firmware image \(testing, accepted or rejected\)\.

`OTA_GetImageState`  
Gets the state of the currently running microcontroller firmware image \(testing, accepted or rejected\)\.

`OTA_CheckForUpdate`  
Requests the next available OTA update from the OTA Update service\.

`OTA_Suspend`  
Suspend all OTA Agent operations\.

`OTA_Resume`  
Resume OTA Agent operations\.

## API reference<a name="freertos-ota-api"></a>

For more information, see the [ OTA Agent API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__ota__agent_8h.html)\.

## Example usage<a name="freertos-ota-example"></a>

A typical OTA\-capable device application using the MQTT protocol drives the OTA Agent by using the following sequence of API calls\.

1. Connect to the AWS IoT MQTT broker\. For more information, see [MQTT library, version 1\.x\.x](freertos-lib-cloud-mqtt.md)\.

1. Initialize the OTA Agent by calling `OTA_AgentInit`\. Your application may define a custom OTA callback function or use the default callback by specifying a NULL callback function pointer\. You must also supply an initialization timeout\.

   The callback implements application\-specific logic that executes after completing an OTA update job\. The timeout defines how long to wait for the initialization to complete\.

1. If `OTA_AgentInit` timed out before the Agent was ready, you can call `OTA_GetAgentState` to confirm that the Agent is initialized and operating as expected\.

1. When the OTA update is complete, FreeRTOS calls the job completion callback with one of the following events: `accepted`, `rejected`, or `self test`\.

1. If the new firmware image has been rejected \(for example, due to a validation error\), the application can typically ignore the notification and wait for the next update\.

1. If the update is valid and has been marked as accepted, call `OTA_ActivateNewImage` to reset the device and boot the new firmware image\.

## Porting<a name="freertos-ota-porting"></a>

For information about porting OTA functionality to your platform, see [Porting the OTA Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html) in the FreeRTOS Porting Guide\.
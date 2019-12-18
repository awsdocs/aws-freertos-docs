# Amazon FreeRTOS AWS IoT Device Shadow Library<a name="freertos-lib-cloud-shadows"></a>

## Overview<a name="freertos-shadow-overview"></a>

The Amazon FreeRTOS Device Shadow APIs define functions to create, update, and delete AWS IoT Device Shadows\. For more information about AWS IoT Device Shadows, see [Device Shadow Service for AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\. The Device Shadow service is accessed using the MQTT protocol\. The Amazon FreeRTOS Device Shadow API works with the MQTT API to handle the details of working with the MQTT protocol\.

## Dependencies and Requirements<a name="freertos-shadow-dependencies"></a>

To use AWS IoT Device Shadows with Amazon FreeRTOS, you need to register your device as an AWS IoT thing\. Your thing must have a certificate with a policy that permits accessing the device's shadow\. For more information, see [AWS IoT Getting Started](https://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\. For an example policy for Amazon FreeRTOS, see the [AWS IoT Device Shadow Demo Application](shadow-demo.md)\.

An example client credentials header file is located at `<amazon-freertos>/demos/include/aws_clientcredential.h`\. Make sure that you set values for the following constants in that header file:

`clientcredentialMQTT_BROKER_ENDPOINT`  
Your AWS IoT endpoint\.

`clientcredentialIOT_THING_NAME`  
The name of your IoT thing\.

`clientcredentialWIFI_SSID`  
The SSID of your Wi\-Fi network\.

`clientcredentialWIFI_PASSWORD`  
Your Wi\-Fi password\.

`clientcredentialWIFI_SECURITY`  
The type of Wi\-Fi security used by your network\.

`keyCLIENT_CERTIFICATE_PEM`  
The certificate PEM associated with your IoT thing\.

`keyCLIENT_PRIVATE_KEY_PEM`  
The private key PEM associated with your IoT thing\.

This file is included in `aws_shadow_lightbulb_on_off.c` \(the [Device Shadow demo application](shadow-demo.md)\)\.

If you are developing your own application, you need to include the `aws_client_credentials.h` header file in the application, and then pass the credentials as `MQTTAgentConnectParams` to `SHADOW_ClientConnect` to connect to AWS IoT over MQTT\. Make sure that you specify your device's registered AWS IoT thing name for the `pucClientId` field of `MQTTAgentConnectParams`, or the Device Shadow client will not connect\.

Before running the application, make sure the Amazon FreeRTOS MQTT library is installed on your device\. For more information, see [MQTT library](freertos-mqtt-2.md)\. 

Also make sure that the MQTT buffers are large enough to contain the shadow JSON files\. The maximum size for a device shadow document is 8 KB\. All default settings for the device shadow API can be set in the `aws_shadow_config_defaults.h` file\. You can modify any of these settings in the `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_shadow_config.h` file\.

**Important**  
The JSON format that you define for your Device Shadow tasks must include a `clientToken` field\. The `clientToken` can take any unique value\. For example, the `aws_shadow_lightbulb_on_off.c` demo application uses `token-%d`, where *%d* is the RTOS tick count at the time the JSON document is generated\.  
If the JSON format does not include a `clientToken` field, calls to SHADOW\_Delete\(\), SHADOW\_Get\(\), and SHADOW\_Update\(\) will timeout\.

## API Reference<a name="freertos-shadow-api"></a>

For a full API reference, see [Device Shadow C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__shadow_8h.html)\.

## Example Usage<a name="freertos-shadow-example"></a>

1. Use the `SHADOW_ClientCreate` API to create a shadow client\. For most applications, the only field to fill is `xCreateParams.xMQTTClientType = eDedicatedMQTTClient`\.

1. Establish an MQTT connection by calling the `SHADOW_ClientConnect` API, passing the client handle returned by `SHADOW_ClientCreate`\.

1. Call the `SHADOW_RegisterCallbacks` API to configure callbacks for shadow update, get, and delete\.

After a connection is established, you can use the following APIs to work with the device shadow:

`SHADOW_Delete`  
Delete the device shadow\.

`SHADOW_Get`  
Get the current device shadow\.

`SHADOW_Update`  
Update the device shadow\.

**Note**  
When you are done working with the device shadow, call `SHADOW_ClientDisconnect` to disconnect the shadow client and free system resources\.
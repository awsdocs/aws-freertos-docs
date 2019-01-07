# Amazon FreeRTOS AWS IoT Device Shadow Library<a name="freertos-lib-cloud-shadows"></a>

## Overview<a name="freertos-shadow-overview"></a>

The Amazon FreeRTOS device shadow APIs define functions to create, update, and delete device shadows\. For more information about Amazon FreeRTOS device shadows, see [Device Shadows](http://docs.aws.amazon.com/iot/latest/developerguide/iot-thing-shadows.html)\. Device shadows are accessed using the MQTT protocol\. The FreeRTOS device shadow API works with the MQTT API and handles the details of working with the MQTT protocol\.

The source files for the Amazon FreeRTOS AWS IoT device shadow library are located in [https://github.com/aws/amazon-freertos/blob/master/lib/shadow](https://github.com/aws/amazon-freertos/blob/master/lib/shadow)\.

## Dependencies and Requirements<a name="freertos-shadow-dependencies"></a>

To use AWS IoT Device Shadows with Amazon FreeRTOS, you need to create a thing in AWS IoT, including a certificate and policy\. For more information, see [AWS IoT Getting Started](http://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\. You must set values for the following constants in the `AmazonFreeRTOS/demos/common/include/aws_client_credentials.h` file:

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
The private key PEM associated with your IoT thing\. For more information, see [Configure Your Project](getting_started_ti.md#ti-freertos-config-project)\.

Make sure the Amazon FreeRTOS MQTT library is installed on your device\. For more information, see [Amazon FreeRTOS MQTT Library \(Legacy\)](freertos-lib-cloud-mqtt.md)\. Make sure that the MQTT buffers are large enough to contain the shadow JSON files\. The maximum size for a device shadow document is 8 KB\. All default settings for the device shadow API can be set in the `lib\include\private\aws_shadow_config_defaults.h` file\. You can modify any of these settings in the `demos/<platform>/common/config_files/aws_shadow_config.h` file\.

You must have an IoT thing registered with AWS IoT, including a certificate with a policy that permits accessing the device shadow\. For more information, see [AWS IoT Getting Started](http://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\.

## Source and Header Files<a name="freertos-shadow-source"></a>

```
Amazon FreeRTOS
|
+ - lib    
    |
    + - shadow
    |   + - aws_shadow.c
    |   + - aws_shadow_json.c
    + - include
        + - aws_shadow.h
        + - private
            + - aws_shadow_config_defaults.h
            + - aws_shadow_json.h
```

## API Reference<a name="freertos-shadow-api"></a>

For a full API reference, see [Device Shadow API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__shadow_8h.html)\.

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
# Amazon FreeRTOS AWS IoT Greengrass Discovery Library<a name="freertos-lib-gg-connectivity"></a>

## Overview<a name="freertos-gg-overview"></a>

The AWS IoT Greengrass Discovery library is used by your microcontroller devices to discover a Greengrass core on your network\. Using the AWS IoT Greengrass Discovery APIs, your device can send messages to a Greengrass core after it finds the core's endpoint\.

The source files for the Amazon FreeRTOS AWS IoT Greengrass library are located in [https://github.com/aws/amazon-freertos/blob/master/lib/greengrass](https://github.com/aws/amazon-freertos/blob/master/lib/greengrass)\.

## Dependencies and Requirements<a name="freertos-gg-dependencies"></a>

To use the Greengrass Discovery library, you must create a thing in AWS IoT, including a certificate and policy\. For more information, see [AWS IoT Getting Started](http://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\. 

You must set values for the following constants in the `AmazonFreeRTOS\demos\common\include\aws_clientcredential.h` file:

`clientcredentialMQTT_BROKER_ENDPOINT`  
Your AWS IoT endpoint\.

`clientcredentialIOT_THING_NAME`  
The name of your IoT thing\.

`clientcredentialWIFI_SSID`  
The SSID for your Wi\-Fi network\.

`clientcredentialWIFI_PASSWORD`  
Your Wi\-Fi password\.

`clientcredentialWIFI_SECURITY`  
The type of security used by your Wi\-Fi network\.

You must also set values for the following constants in the `AmazonFreeRTOS\demos\common\include\aws_clientcredential_keys.h` file:

`keyCLIENT_CERTIFICATE_PEM`  
The certificate PEM associated with your thing\.

`keyCLIENT_PRIVATE_KEY_PEM`  
The private key PEM associated with your thing\.

You must have a Greengrass group and core device set up in the console\. For more information, see [Getting Started with AWS IoT Greengrass](http://docs.aws.amazon.com/greengrass/latest/developerguide/)\.

Although the MQTT library is not required for Greengrass connectivity, we strongly recommend you install it\. The library can be used to communicate with the Greengrass core after it has been discovered\.

## Source and Header Files<a name="freertos-gg-source"></a>

```
Amazon FreeRTOS
|
+ - lib
    + - greengrass
    |   + ─ aws_greengrass_discovery.c
    |   + ─ aws_helper_secure_connect.c
    + - include
        + - aws_greengrass_discovery.h
        + - private
            + - aws_ggd_config_defaults.h
```

## API Reference<a name="freertos-gg-api"></a>

For a full API reference, see [Greengrass API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__greengrass__discovery_8h.html)\.

## Example Usage<a name="freertos-gg-example"></a>

### Greengrass Workflow<a name="freertos-lib-gg-workflow"></a>

The MCU device initiates the discovery process by requesting from AWS IoT a JSON file that contains the Greengrass core connectivity parameters\. There are two methods for retrieving the Greengrass core connectivity parameters from the JSON file:
+ Automatic selection iterates through all of the Greengrass cores listed in the JSON file and connects to the first one available\. 
+ Manual selection uses the information in `aws_ggd_config.h` to connect to the specified Greengrass core\.

### How to Use the Greengrass API<a name="freertos-lib-gg-api"></a>

All default configuration options for the Greengrass API are defined in `lib\include\private\aws_ggd_config_defaults.h`\. You can override any of these settings in the header files in `lib\include\`\.

If only one Greengrass core is present, call `GGD_GetGGCIPandCertificate` to request the JSON file with Greengrass core connectivity information\. When `GGD_GetGGCIPandCertificate` is returned, the `pcBuffer` parameter contains the text of the JSON file\. The `pxHostAddressData` parameter contains the IP address and port of the Greengrass core to which you can connect\.

For more customization options, like dynamically allocating certificates, you must call the following APIs:

`GGD_JSONRequestStart`  
Makes an HTTP GET request to AWS IoT to initiate the discovery request to discover a Greengrass core\. `GD_SecureConnect_Send` is used to send the request to AWS IoT\.

`GGD_JSONRequestGetSize`  
Gets the size of the JSON file from the HTTP response\.

`GGD_JSONRequestGetFile`  
Gets the JSON object string\. `GGD_JSONRequestGetSize` and `GGD_JSONRequestGetFile` use `GGD_SecureConnect_Read` to get the JSON data from the socket\. `GGD_JSONRequestStart`, `GGD_SecureConnect_Send`, `GGD_JSONRequestGetSize` must be called to receive the JSON data from AWS IoT\.

`GGD_GetIPandCertificateFromJSON`  
Extracts the IP address and the Greengrass core certificate from the JSON data\. You can turn on automatic selection by setting the `xAutoSelectFlag` to `True`\. Automatic selection finds the first core device your FreeRTOS device can connect to\. To connect to a Greengrass core, call the `GGD_SecureConnect_Connect` function, passing in the IP address, port, and certificate of the core device\. To use manual selection, set the following fields of the `HostParameters_t` parameter:    
`pcGroupName`  
The ID of the Greengrass group to which the core belongs\. You can use the `aws greengrass list-groups` CLI command to find the ID of your Greengrass groups\.  
`pcCoreAddress`  
The ARN of the Greengrass core to which you are connecting\.
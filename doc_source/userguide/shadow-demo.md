# AWS IoT Device Shadow demo application<a name="shadow-demo"></a>

## Introduction<a name="shadow-demo-introduction"></a>

This demo shows how to use the AWS IoT Device Shadow library to connect to the [AWS Device Shadow service](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\. It uses the [coreMQTT library](coremqtt.md) to establish an MQTT connection with TLS \(Mutual Authentication\) to the AWS IoT MQTT Broker and the  coreJSON library parser to parse shadow documents received from the AWS Shadow service\. The demo shows basic shadow operations, such as how to update a shadow document and how to delete a shadow document\. The demo also shows how to register a callback function with the coreMQTT library to handle messages like the shadow `/update` and `/update/delta` messages that are sent from the AWS IoT Device Shadow service\.

This demo is intended as a learning exercise only because the request to update the shadow document \(state\) and the update response are done by the same application\. In a realistic production scenario, an external application would request an update of the state of the device remotely, even if the device is not currently connected\. The device will acknowledge the update request when it is connected\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Functionality<a name="shadow-demo-functionality"></a>

The demo creates a single application task that loops through a set of examples that demonstrate shadow `/update` and `/update/delta` callbacks to simulate toggling a remote device's state\. It sends a shadow update with the new `desired` state and waits for the device to change its `reported` state in response to the new `desired` state\. In addition, a shadow `/update` callback is used to print the changing shadow states\. This demo also uses a secure MQTT connection to the AWS IoT MQTT Broker, and assumes there is a `powerOn` state in the device shadow\.

The demo performs the following operations:

1. Establish an MQTT connection by using the helper functions in `shadow_demo_helpers.c`\.

1. Assemble MQTT topic strings for device shadow operations, using macros defined by the AWS IoT Device Shadow library\.

1. Publish to the MQTT topic used for deleting a device shadow to delete any existing device shadow\.

1. Subscribe to the MQTT topics for `/update/delta`, `/update/accepted` and `/update/rejected` using helper functions in `shadow_demo_helpers.c`\.

1. Publish a desired state of `powerOn` using helper functions in `shadow_demo_helpers.c`\. This will cause an `/update/delta` message to be sent to the device\.

1. Handle incoming MQTT messages in `prvEventCallback`, and determine whether the message is related to the device shadow by using a function defined by the AWS IoT Device Shadow library \(`Shadow_MatchTopic`\)\. If the message is a device shadow `/update/delta` message, then the main demo function will publish a second message to update the reported state to `powerOn`\. If an `/update/accepted` message is received, verify that it has the same `clientToken` as previously published in the update message\. That will mark the end of the demo\.

![\[shadow demo terminal output\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/shadow-demo-output.png)

The demo can be found in the file `freertos/demos/device_shadow_for_aws/shadow_demo_main.c` or on [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/device_shadow_for_aws/shadow_demo_main.c)\.

The following screenshot shows the expected output when the demo succeeds\.

![\[shadow demo terminal output showing success\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/shadow-demo-screenshot.png)

## Connect to the AWS IoT MQTT broker<a name="shadow-demo-connect-mqtt"></a>

To connect to the AWS IoT MQTT broker, we use the same method as `MQTT_Connect()` in the [coreMQTT mutual authentication demo](mqtt-demo-ma.md)\.

## Delete the shadow document<a name="shadow-demo-delete-document"></a>

To delete the shadow document, call `xPublishToTopic` with an empty message, using macros defined by the AWS IoT Device Shadow library\. This uses `MQTT_Publish` to publish to the `/delete` topic\. The following code section shows how this is done in the function `prvShadowDemoTask`\.

```
/* First of all, try to delete any Shadow document in the cloud. */
returnStatus = PublishToTopic( SHADOW_TOPIC_STRING_DELETE( THING_NAME ),
                               SHADOW_TOPIC_LENGTH_DELETE( THING_NAME_LENGTH ),
                               pcUpdateDocument,
                               0U );
```

## Subscribe to shadow topics<a name="shadow-demo-subscribe"></a>

Subscribe to the Device Shadow topics to receive notifications from the AWS IoT broker about shadow changes\. The Device Shadow topics are assembled by macros defined in the Device Shadow library\. The following code section shows how this is done in the `prvShadowDemoTask` function\.

```
/* Then try to subscribe shadow topics. */
if( returnStatus == EXIT_SUCCESS )
{
    returnStatus = SubscribeToTopic( 
                     SHADOW_TOPIC_STRING_UPDATE_DELTA( THING_NAME ),
                     SHADOW_TOPIC_LENGTH_UPDATE_DELTA( THING_NAME_LENGTH ) );
}

if( returnStatus == EXIT_SUCCESS )
{
    returnStatus = SubscribeToTopic( 
                     SHADOW_TOPIC_STRING_UPDATE_ACCEPTED( THING_NAME ),
                     SHADOW_TOPIC_LENGTH_UPDATE_ACCEPTED( THING_NAME_LENGTH ) );
}

if( returnStatus == EXIT_SUCCESS )
{
    returnStatus = SubscribeToTopic( 
                     SHADOW_TOPIC_STRING_UPDATE_REJECTED( THING_NAME ),
                     SHADOW_TOPIC_LENGTH_UPDATE_REJECTED( THING_NAME_LENGTH ) );
}
```

## Send Shadow Updates<a name="shadow-demo-send-updates"></a>

To send a shadow update, the demo calls `xPublishToTopic` with a message in JSON format, using macros defined by the Device Shadow library\. This uses `MQTT_Publish` to publish to the `/delete` topic\. The following code section shows how this is done in the `prvShadowDemoTask` function\.

```
#define SHADOW_REPORTED_JSON    \
    "{"                         \
    "\"state\":{"               \
    "\"reported\":{"            \
    "\"powerOn\":%01d"          \
    "}"                         \
    "},"                        \
    "\"clientToken\":\"%06lu\"" \
    "}"
snprintf( pcUpdateDocument,
          SHADOW_REPORTED_JSON_LENGTH + 1,
          SHADOW_REPORTED_JSON,
           ( int ) ulCurrentPowerOnState,
           ( long unsigned ) ulClientToken );

xPublishToTopic( SHADOW_TOPIC_STRING_UPDATE( THING_NAME ),
                 SHADOW_TOPIC_LENGTH_UPDATE( THING_NAME_LENGTH ),
                 pcUpdateDocument,
                 ( SHADOW_DESIRED_JSON_LENGTH + 1 ) );
```

## Handle shadow delta messages and shadow update messages<a name="shadow-demo-delta-and-update"></a>

The user callback function, that was registered to the [coreMQTT Client Library](https://freertos-wordpress.corp.amazon.com/shadow/device-shadow-demo.html#handle-shadow-messages) using the `MQTT_Init` function, will notify us about an incoming packet event\. See the callback function [ prvEventCallback](https://github.com/aws/amazon-freertos/blob/202012.00/demos/device_shadow_for_aws/shadow_demo_main.c#L655-L737) on GitHub\.

The callback function confirms the incoming packet is of type `MQTT_PACKET_TYPE_PUBLISH`, and uses the Device Shadow Library API `Shadow_MatchTopic` to confirm that the incoming message is a shadow message\.

If the incoming message is a shadow message with type `ShadowMessageTypeUpdateDelta`, then we call [ prvUpdateDeltaHandler](https://github.com/aws/amazon-freertos/blob/202012.00/demos/device_shadow_for_aws/shadow_demo_main.c#L655-L737) to handle this message\. The handler `prvUpdateDeltaHandler` uses the  coreJSON library to parse the message to get the delta value for the `powerOn` state and compares this against the current device state maintained locally\. If those are different, the local device state is updated to reflect the new value of the `powerOn` state from the shadow document\.

If the incoming message is a shadow message with type `ShadowMessageTypeUpdateAccepted`, then we call [ prvUpdateAcceptedHandler](https://github.com/aws/amazon-freertos/blob/202012.00/demos/device_shadow_for_aws/shadow_demo_main.c#L568-L651) to handle this message\. The handler `prvUpdateAcceptedHandler` parses the message using the  coreJSON library to get the `clientToken` from the message\. This handler function checks that the client token from the JSON message matches the client token used by the application\. If it doesn't match, the function logs a warning message\.
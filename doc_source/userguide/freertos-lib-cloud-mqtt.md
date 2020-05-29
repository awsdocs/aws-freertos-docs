# MQTT library, version 1\.0\.0<a name="freertos-lib-cloud-mqtt"></a>

## Overview<a name="freertos-mqtt-overview"></a>

FreeRTOS includes an open source MQTT client library that you can use to create applications that publish and subscribe to MQTT topics, as MQTT clients on a network\.

Version 2\.0\.0 of the MQTT Library is available for FreeRTOS versions 201960\.00 and later\. This newer library is compatible with all transport types, meaning you can use it with Bluetooth Low Energy and TCP/IP\. For more information, see [MQTT library, version 2\.0\.0](freertos-mqtt-2.md)\.

### The FreeRTOS MQTT Agent<a name="freertos-mqtt-agent"></a>

FreeRTOS also includes an open source daemon, called the FreeRTOS MQTT Agent, that manages the MQTT library for you\. The MQTT Agent provides a simple interface to connect, publish, and subscribe to MQTT topics with the underlying MQTT library\.

The MQTT Agent runs in a separate FreeRTOS task and automatically sends regular keep\-alive messages, as documented by the MQTT protocol specification\. All the MQTT APIs are blocking and take a timeout parameter, which is the maximum amount of time the API waits for the corresponding operation to complete\. If the operation does not complete in the provided time, the API returns timeout error code\.

## Dependencies and requirements<a name="freertos-mqtt-dependencies"></a>

The FreeRTOS MQTT library uses the [Secure Sockets library](secure-sockets.md) and the FreeRTOS Buffer Pool library\. If the MQTT Agent connects to a secure MQTT broker, the library also uses the [Transport Layer Security](security-tls.md)\.

## Features<a name="freertos-mqtt-features"></a>

### Callback<a name="freertos-lib-cloud-mqtt-callback"></a>

You can specify an optional callback that is invoked whenever the MQTT Agent is disconnected from the broker or whenever a publish message is received from the broker\. The received publish message is stored in a buffer taken from the central buffer pool\. This message is passed to the callback\. This callback runs in the context of the MQTT task and therefore must be quick\. If you need to do longer processing, you must take the ownership of the buffer by returning `pdTRUE` from the callback\. You must then return the buffer back to the pool whenever you are done by calling `FreeRTOS_Agent_ReturnBuffer`\.

### Subscription management<a name="freertos-lib-cloud-mqtt-sub-mgmt"></a>

Subscription management enables you to register a callback per subscription filter\. You supply this callback while subscribing\. It is invoked whenever a publish message received on a topic matches the subscribed topic filter\. The buffer ownership works the same way as described in the case of generic callback\.

### MQTT task wakeup<a name="freertos-lib-cloud-mqtt-wakeup"></a>

MQTT task wakeup wakes up whenever the user calls an API to perform any operation or whenever a publish message is received from the broker\. This asynchronous wakeup upon receipt of a publish message is possible on platforms that are capable of informing the host MCU about the data received on a connected socket\. Platforms that do not have this capability require the MQTT task to continuously poll for the received data on the connected socket\. To ensure the delay between receiving a publish message and invoking the callback is minimal, the `mqttconfigMQTT_TASK_MAX_BLOCK_TICKS` macro controls the maximum time an MQTT task can remain blocked\. This value must be short for the platforms that lack the capability to inform the host MCU about received data on a connected socket\.

## Major configurations<a name="freertos-lib-cloud-mqtt-config"></a>

These flags can be specified during the MQTT connection request:
+ `mqttconfigKEEP_ALIVE_ACTUAL_INTERVAL_TICKS`: The frequency of the keep\-alive messages sent\.
+ `mqttconfigENABLE_SUBSCRIPTION_MANAGEMENT`: Enable subscription management\.
+ `mqttconfigMAX_BROKERS`: Maximum number of simultaneous MQTT clients\.
+ `mqttconfigMQTT_TASK_STACK_DEPTH`: The task stack depth\.
+ `mqttconfigMQTT_TASK_PRIORITY`: The priority of the MQTT task\.
+ `mqttconfigRX_BUFFER_SIZE`: Length of the buffer used to receive data\.
+ `mqttagentURL_IS_IP_ADDRESS`: Set this bit in `xFlags` if the provided URL is an IP address\.
+ `mqttagentREQUIRE_TLS`: Set this bit in `xFlags` to use TLS\.
+ `mqttagentUSE_AWS_IOT_ALPN_443`: Set this bit in `xFlags` to use AWS IoT support for MQTT over TLS port 443\.

For more information about ALPN, see the [AWS IoT Protocols](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html) in the AWS IoT Developer Guide and the [MQTT with TLS Client Authentication on Port 443: Why It Is Useful and How It Works](http://aws.amazon.com/blogs/iot/mqtt-with-tls-client-authentication-on-port-443-why-it-is-useful-and-how-it-works) blog post on the Internet of Things on AWS blog\. 

## Optimization<a name="freertos-mqtt-optimization"></a>

### Processing received packets without delay<a name="freertos-mqtt-optimization-packets"></a>

The task that implements the MQTT Agent spends most of its time in the Blocked state \(so not using any CPU cycles\) waiting for events to process\. MQTT throughput is maximized by unblocking the Agent task as soon as an MQTT packet is received from the network\. If that is done the received packet is processed at the earliest opportunity\. If that is not done the received packet will not be processed until the MQTT Agent leaves the Blocked state for another reason\. 

The MQTT Agent is removed from the Blocked state by the execution of a callback that is installed by the MQTT Agent calling SOCKETS\_SetSockOpt\(\) with the lOptionName parameter set to SOCKETS\_SO\_WAKEUP\_CALLBACK\. Links to the secure sockets documentation are needed here\. If you are using the FreeRTOS\+TCP TCP/IP stack the callback is executed at the correct time provided ipconfigSOCKET\_HAS\_USER\_WAKE\_CALLBACK is set to 1 in FreeRTOSIPConfig\.h \(which is the TCP/IP stack's configuration file\)\. If you are not using the FreeRTOS\+TCP TCP/IP stack then the secure sockets ensure this functionality is included in your implementation of the secure sockets abstraction layer for the stack in use\.

 If the TCP/IP stack cannot unblock the MQTT Agent as soon as data is received then the maximum time between a packet being received and the packet being processed is set by the mqttconfigMQTT\_TASK\_MAX\_BLOCK\_TICKS constant\. 

### Minimizing RAM consumption<a name="freertos-mqtt-optimization-ram"></a>

The following configuration constants directly affect the amount of RAM required by the MQTT Agent:
+ mqttconfigMQTT\_TASK\_STACK\_DEPTH
+ mqttconfigMQTT\_TASK\_STACK\_DEPTH
+ mqttconfigMAX\_BROKERS
+ mqttconfigMAX\_PARALLEL\_OPS
+ mqttconfigRX\_BUFFER\_SIZE

You should set these constants to the minimum values possible\.

### Requirements and usage restrictions<a name="freertos-mqtt-requirements-restrictions"></a>

#### <a name="w14aab9c27c13b7b3"></a>

The MQTT Agent task is created using the xTaskCreateStatic\(\) API function \- so the task's stack and control block are statically allocated at compile time\. That ensures the MQTT Agent can be used in applications that do not allow dynamic memory allocation, but does mean there is a dependency on configSUPPORT\_STATIC\_ALLOCATION being set to 1 in [`FreeRTOSConfig.h`](dev-guide-freertos-kernel.md#freertos-config)\. 

#### <a name="w14aab9c27c13b7b5"></a>

he MQTT Agent uses the FreeRTOS direct to task notification feature\. Calling an MQTT Agent API function may change the calling task's notification value and state\. 

#### <a name="w14aab9c27c13b7b7"></a>

MQTT packets are stored in buffers provided by the Buffer Pool module\. It is highly recommended to ensure the number of buffers in the pool is at least double the number of MQTT transactions that will be in progress at any one time\.

## Developer support<a name="freertos-mqtt-support"></a>

### `mqttconfigASSERT`<a name="w14aab9c27c15b3"></a>

mqttconfigASSERT\(\) is equivalent to, and used in exactly the same way as, the FreeRTOS configASSERT\(\) macro\. If you want assert statements in the MQTT Agent then define mqttconfigASSERT\(\)\. If you do not want assert statements in the MQTT Agent then leave mqttconfigASSERT\(\) undefined\. If you define mqttconfigASSERT\(\) to call the FreeRTOS configASSERT\(\), as shown below, then the MQTT Agent will only include assert statements if the FreeRTOS configASSERT\(\) is defined\. 

`#define mqttconfigASSERT( x ) configASSERT( x )`

### `mqttconfigENABLE_DEBUG_LOGS`<a name="w14aab9c27c15b5"></a>

#### <a name="w14aab9c27c15b5b3"></a>

Set `mqttconfigENABLE_DEBUG_LOGS` to **1** to print debug logs via calls to vLoggingPrintf\(\)\.

## Initialization<a name="freertos-mqtt-initialization"></a>

Both the MQTT Agent and its dependent libraries must be initialized, as shown below, before attempting MQTT communication\. Initialize the libraries after a network connection is established\.

```
BaseType_t SYSTEM_Init() { BaseType_t xResult = pdPASS; /* The bufferpool libraries provides the buffers use to store MQTT packets.*/
                        xResult = BUFFERPOOL_Init(); 
                        if( xResult == pdPASS ) { /* Create the MQTT Agent task. */
                        xResult = MQTT_AGENT_Init(); } 
                        if( xResult == pdPASS ) { /* Initialize the secure sockets abstraction layer.*/
                        xResult = SOCKETS_Init(); }
                        return xResult; }
```

## API reference<a name="freertos-mqtt-api"></a>

For a full API reference, see [ MQTT \(v1\.0\.0\) Library API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__lib_8h.html) and [ MQTT \(v1\) Agent API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__agent_8h.html)\.

## Porting<a name="freertos-mqtt-porting"></a>

The Secure Sockets abstraction layer that the MQTT Agent calls must be ported to specific architectures\. For more information, see [Porting the Secure Sockets Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ss.html) in the FreeRTOS Porting Guide\.
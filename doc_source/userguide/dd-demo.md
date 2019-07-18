# AWS IoT Device Defender Demo<a name="dd-demo"></a>

Amazon FreeRTOS includes a single\-threaded demo application that collects some AWS IoT Device Defender metrics for a device and publishes them to an MQTT topic\. This demo is defined in `<amazon-freertos>/demos/defender/aws_iot_defender_demo.c`\.

Before you can run the Device Defender demo, you must complete the getting started [First Steps](freertos-prereqs.md) to set up AWS IoT and Amazon FreeRTOS so your device can communicate with the AWS cloud\.

Open `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_DEFENDER_DEMO_ENABLED`\.

When you build, flash, and run Amazon FreeRTOS on your device with the Device Defender demo enabled, the following output should appear:

```
13 199 [iot_thread] [INFO ][DEMO][199] ----Device Defender Demo Start----.

14 199 [iot_thread] [INFO ][Defender][199] Metrics are successfully updated.
15 199 [iot_thread] [INFO ][Defender][199] Period has been set to 300 seconds successfully.
16 199 [iot_thread] [INFO ][Defender][199] Defender agent has successfully started.
17 250 [iot_thread] [INFO ][DEMO][250] expected ip: 34.208.133.153
18 570 [iot_thread] [INFO ][MQTT][570] Establishing new MQTT connection.
19 575 [iot_thread] [INFO ][MQTT][575] (MQTT connection 00E5CAC0, CONNECT operation 00E5CC50) Waiting for operation completion.
20 670 [iot_thread] [INFO ][MQTT][670] (MQTT connection 00E5CAC0, CONNECT operation 00E5CC50) Wait complete with result SUCCESS.
21 670 [iot_thread] [INFO ][MQTT][670] New MQTT connection 010BA79C established.
22 670 [iot_thread] [INFO ][MQTT][670] (MQTT connection 00E5CAC0) SUBSCRIBE operation scheduled.
23 670 [iot_thread] [INFO ][MQTT][670] (MQTT connection 00E5CAC0, SUBSCRIBE operation 00E5CC50) Waiting for operation completion.
24 710 [iot_thread] [INFO ][MQTT][710] (MQTT connection 00E5CAC0, SUBSCRIBE operation 00E5CC50) Wait complete with result SUCCESS.
25 711 [iot_thread] [INFO ][MQTT][711] (MQTT connection 00E5CAC0) MQTT PUBLISH operation queued.
26 870 [iot_thread] [INFO ][Defender][870] Metrics report was accepted by defender service.
27 870 [iot_thread] [INFO ][DEMO][870] User's callback is invoked on event 0.
28 870 [iot_thread] [INFO ][DEMO][870]
Published metrics report.
29 870 [iot_thread] [INFO ][DEMO][870]
Received MQTT message.
30 3711 [iot_thread] [INFO ][MQTT][3711] (MQTT connection 00E5CAC0) Disconnecting connection.
31 3711 [iot_thread] [INFO ][MQTT][3711] (MQTT connection 00E5CAC0, DISCONNECT operation 00E5CC50) Waiting for operation completion.
32 3712 [iot_thread] [INFO ][MQTT][3712] (MQTT connection 00E5CAC0, DISCONNECT operation 00E5CC50) Wait complete with result SUCCESS.
33 3712 [iot_thread] [INFO ][MQTT][3712] (MQTT connection 00E5CAC0) Connection disconnected.
34 3712 [iot_thread] [INFO ][MQTT][3712] (MQTT connection 00E5CAC0) Network connection closed.
35 3751 [iot_thread] [INFO ][MQTT][3751] (MQTT connection 00E5CAC0) Network connection destroyed.
36 3752 [iot_thread] [INFO ][Defender][3752] Defender agent has stopped.
37 3752 [iot_thread] [INFO ][DEMO][3752] ----Device Defender Demo End----.
```
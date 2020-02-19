# AWS IoT Device Defender Demo<a name="dd-demo"></a>

FreeRTOS includes a single\-threaded demo application that collects some AWS IoT Device Defender metrics for a device and publishes them to an MQTT topic\. This demo is defined in `<freertos>/demos/defender/aws_iot_defender_demo.c`\.

Before you can run the Device Defender demo, you must complete the getting started [First Steps](freertos-prereqs.md) to set up AWS IoT and FreeRTOS so your device can communicate with the AWS cloud\.

 Open `<freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_DEFENDER_DEMO_ENABLED`\.

When you build, flash, and run FreeRTOS on your device with the Device Defender demo enabled, the following output should appear:

```
12 343 [iot_thread] [INFO ][DEMO][343] ----Device Defender Demo Start----
13 343 [iot_thread] [INFO ][MQTT][343] MQTT library successfully initialized.
14 343 [iot_thread] [INFO ][Defender][343] Metrics are successfully updated.
15 343 [iot_thread] [INFO ][Defender][343] Period has been set to 300 seconds successfully.
16 343 [iot_thread] [INFO ][DEMO][343] Defender Thing Name is Thing-1 (length 7).
17 711 [iot_thread] [INFO ][MQTT][711] Establishing new MQTT connection.
18 711 [iot_thread] [INFO ][MQTT][711] Anonymous metrics (SDK language, SDK version) will be provided to AWS IoT. Recompile with AWS_IOT_MQTT_ENABLE_METRICS set to 0 to disable.
19 711 [iot_thread] [INFO ][MQTT][711] (MQTT connection 00530B30, CONNECT operation 00530CC0) Waiting for operation completion.
20 771 [iot_thread] [INFO ][MQTT][771] (MQTT connection 00530B30, CONNECT operation 00530CC0) Wait complete with result SUCCESS.
21 771 [iot_thread] [INFO ][MQTT][771] New MQTT connection 0203FA0C established.
22 771 [iot_thread] [INFO ][MQTT][771] (MQTT connection 00530B30) SUBSCRIBE operation scheduled.
23 771 [iot_thread] [INFO ][MQTT][771] (MQTT connection 00530B30, SUBSCRIBE operation 00530E30) Waiting for operation completion.
24 811 [iot_thread] [INFO ][MQTT][811] (MQTT connection 00530B30, SUBSCRIBE operation 00530E30) Wait complete with result SUCCESS.
25 811 [iot_thread] [INFO ][Defender][811] Defender agent has successfully started.
26 812 [iot_thread] [INFO ][MQTT][812] (MQTT connection 00530B30) MQTT PUBLISH operation queued.
27 932 [iot_thread] [INFO ][Defender][932] Metrics report was accepted by defender service.
28 932 [iot_thread] [INFO ][DEMO][932] User's callback is invoked on event: Defender Metrics accepted.
29 932 [iot_thread] [INFO ][DEMO][932] Published metrics report.
30 932 [iot_thread] [INFO ][DEMO][932] Received MQTT message.
31 8811 [iot_thread] [INFO ][Defender][8811] Unsubscribing from MQTT topics
32 8811 [iot_thread] [INFO ][MQTT][8811] (MQTT connection 00530B30) UNSUBSCRIBE operation scheduled.
33 8811 [iot_thread] [INFO ][MQTT][8811] (MQTT connection 00530B30, UNSUBSCRIBE operation 00530F40) Waiting for operation completion.
34 8891 [iot_thread] [INFO ][MQTT][8891] (MQTT connection 00530B30, UNSUBSCRIBE operation 00530F40) Wait complete with result SUCCESS.
35 8891 [iot_thread] [INFO ][Defender][8891] Defender agent has stopped.
35 8891 [iot_thread] [INFO ][MQTT][8891] (MQTT connection 00530B30) Disconnecting connection.
37 8892 [iot_thread] [INFO ][MQTT][8892] (MQTT connection 00530B30, DISCONNECT operation 00530CE0) Waiting for operation completion.
38 8892 [iot_thread] [INFO ][MQTT][8892] (MQTT connection 00530B30, DISCONNECT operation 00530CE0) Wait complete with result SUCCESS.
39 8892 [iot_thread] [INFO ][MQTT][8892] (MQTT connection 00530B30) Connection disconnected.
40 8893 [iot_thread] [INFO ][MQTT][8893] (MQTT connection 00530B30) Network connection closed.
41 8931 [iot_thread] [INFO ][MQTT][8931] (MQTT connection 00530B30) Network connection destroyed.
42 8931 [iot_thread] [INFO ][MQTT][8931] MQTT library cleanup done.
43 8931 [iot_thread] [INFO ][DEMO][8931] ----Device Defender Demo End. Status: SUCCESS----.
```
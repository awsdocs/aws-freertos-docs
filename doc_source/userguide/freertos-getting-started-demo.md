# FreeRTOS demo application<a name="freertos-getting-started-demo"></a>

The demo application in this tutorial is the Hello World MQTT demo defined in the `/demos/mqtt/iot_demo_mqtt.c` file\. It uses the [FreeRTOS MQTT library](freertos-lib-cloud-mqtt.md) to connect to the AWS Cloud and then periodically publish messages to an MQTT topic hosted by the [AWS IoT MQTT broker](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html)\.

Only a single FreeRTOS demo application can run at a time\. When you build a FreeRTOS demo project, the first demo enabled in the `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h` header file is the application that runs\. For this tutorial, you do not need to enable or disable any demos\. The Hello World MQTT demo is enabled by default\.

For more information about the demo applications included with FreeRTOS, see [FreeRTOS Demos](freertos-next-steps.md)\.
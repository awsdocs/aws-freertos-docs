# FreeRTOS demo application<a name="freertos-getting-started-demo"></a>

The demo application in this tutorial is the coreMQTT Agent demo defined in the `freertos/demos/coreMQTT_Agent/mqtt_agent_task.c` file\. It uses  the [coreMQTT library](coremqtt.md) to connect to the AWS Cloud and then periodically publish messages to an MQTT topic hosted by the [ AWS IoT MQTT broker](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html)\.

Only a single FreeRTOS demo application can run at a time\. When you build a FreeRTOS demo project, the first demo enabled in the `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h` header file is the application that runs\. For this tutorial, you do not need to enable or disable any demos\. The coreMQTT Agent demo is enabled by default\.

For more information about the demo applications included with FreeRTOS, see [FreeRTOS demos](freertos-next-steps.md)\.
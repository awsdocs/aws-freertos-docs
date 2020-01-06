# Getting Started with Amazon FreeRTOS<a name="freertos-getting-started"></a>

This Getting Started with Amazon FreeRTOS tutorial shows you how to download and configure Amazon FreeRTOS on a host machine, and then compile and run a simple demo application on a [qualified microcontroller board](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

Throughout this tutorial, we assume that you are familiar with AWS IoT and the AWS IoT console\. If not, we recommend that you complete the [AWS IoT Getting Started](https://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html) tutorial before you continue\.

## Amazon FreeRTOS Demo Application<a name="w12aab7b7"></a>

The demo application in this tutorial is the Hello World MQTT demo defined in the `/demos/mqtt/iot_demo_mqtt.c` file\. It uses the [Amazon FreeRTOS MQTT library](freertos-lib-cloud-mqtt.md) to connect to the AWS Cloud and then periodically publish messages to an MQTT topic hosted by the [AWS IoT MQTT broker](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html)\.

Only a single Amazon FreeRTOS demo application can run at a time\. When you build an Amazon FreeRTOS demo project, the first demo enabled in the `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h` header file is the application that runs\. For this tutorial, you do not need to enable or disable any demos\. The Hello World MQTT demo is enabled by default\.

For more information about the demo applications included with Amazon FreeRTOS, see [Amazon FreeRTOS Demos](freertos-next-steps.md)\.

## First Steps<a name="w12aab7b9"></a>

To get started, see [First Steps](freertos-prereqs.md)\.

## Board\-specific Getting Started Guides<a name="w12aab7c11"></a>

After you complete the [First Steps](freertos-prereqs.md), you can set up your platform's hardware and its software development environment, and then compile and run the demo on your board\. For board\-specific instructions, see the [Board\-specific Getting Started Guides](getting-started-guides.md)\.

## Troubleshooting<a name="w12aab7c13"></a>

For help troubleshooting any issues that you encounter while getting started, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\. For board\-specific troubleshooting tips, see the Getting Started guide for your board in [Board\-specific Getting Started Guides](getting-started-guides.md)\.

## Developing Amazon FreeRTOS Applications<a name="w12aab7c15"></a>

You can use an IDE to edit, debug, compile, flash, and run code on Amazon FreeRTOS\-qualified devices\. Each board\-specific Getting Started guide includes instructions for setting up the IDE for a particular platform\.

You can also use third\-party code editors and debuggers to develop applications, and CMake to build and run the source code\. For more information about using CMake as a build tool for Amazon FreeRTOS development, see [Using CMake with Amazon FreeRTOS](getting-started-cmake.md)\. 
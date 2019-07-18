# Amazon FreeRTOS Demos<a name="freertos-next-steps"></a>

Amazon FreeRTOS includes some demo applications in the `demos` folder, under the main Amazon FreeRTOS directory\. All of the examples that can be executed by Amazon FreeRTOS appear in the `common` folder, under `demos`\. There is also a folder for each Amazon FreeRTOS\-qualified platform under the `demos` folder\. If you use the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), only the target platform you choose has a subdirectory under `demos`\.

Before you try the demo applications, we recommend that you complete the tutorial in [Getting Started with Amazon FreeRTOS](freertos-getting-started.md)\. It shows you how to set up and run the Hello World MQTT demo\.

## Running the Amazon FreeRTOS Demos<a name="running-demos"></a>

The following topics show you how to set up and run the Amazon FreeRTOS demos:
+ [Bluetooth Low Energy Demo Applications](ble-demo.md)
+ [Demo Bootloader for the Microchip Curiosity PIC32MZEF](microchip-bootloader.md)
+ [AWS IoT Device Defender Demo](dd-demo.md)
+ [AWS IoT Greengrass Discovery Demo Application](gg-demo.md)
+ [Over\-the\-Air Updates Demo Application](ota-demo.md)
+ [Secure Sockets Echo Client Demo](secure-sockets-demo.md)
+ [AWS IoT Device Shadow Demo Application](shadow-demo.md)

The `DEMO_RUNNER_RunDemos()` function, located in `<amazon-freertos>/demos/demo_runner/iot_demo_runner.c`, initializes a detached thread on which a single demo application runs\. By default, `DEMO_RUNNER_RunDemos()` only calls the starts the Hello World MQTT demo\. Depending on the configuration you selected when you downloaded Amazon FreeRTOS, and depending on where you downloaded Amazon FreeRTOS, the other example runner functions might start by default\. To enable a demo application, open `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h`, and define the demo that you want to run\.

**Note**  
Be aware that not all combinations of examples work together\. Depending on the combination, the software might fail to execute on the selected target due to memory constraints\. We recommend that you run one demo at a time\.

## Configuring the Demos<a name="configuring-demos"></a>

The demos have been configured to get you started quickly\. You might want to change some of the configurations for your project to create a version that runs on your platform\. You can find configuration files at `vendors/<vendor>/boards/<board>/aws_demos/config_files`\.
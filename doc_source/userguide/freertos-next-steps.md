# Amazon FreeRTOS Demos<a name="freertos-next-steps"></a>

Amazon FreeRTOS includes some demo applications in the `demos` folder, under the main Amazon FreeRTOS directory\. All of the examples that can be executed by Amazon FreeRTOS appear in the `common` folder, under `demos`\. There is also a folder for each Amazon FreeRTOS\-qualified platform under the `demos` folder\. If you use the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), only the target platform you choose has a subdirectory under `demos`\.

Before trying out the demo applications on your own, we recommend that you go through [Getting Started with Amazon FreeRTOS](freertos-getting-started.md), which walks you through setting up and running the Hello World MQTT demo\.

## Running the Amazon FreeRTOS Demos<a name="running-demos"></a>

The following topics show you how to set up and run the Amazon FreeRTOS demos:
+ [Bluetooth Low Energy Demo Applications](ble-demo.md)
+ [Demo Bootloader for the Microchip Curiosity PIC32MZEF](microchip-bootloader.md)
+ [AWS IoT Greengrass Discovery Demo Application](gg-demo.md)
+ [Over\-the\-Air Updates Demo Application](ota-demo.md)
+ [Secure Sockets Echo Client Demo](secure-sockets-demo.md)
+ [AWS IoT Device Shadow Demo Application](shadow-demo.md)

The `DEMO_RUNNER_RunDemos()` function, located in `AmazonFreeRTOS\demos\common\demo_runner\aws_demo_runner.c`, contains code that calls each example\. By default, `DEMO_RUNNER_RunDemos()` only calls the `vStartMQTTEchoDemo()` function, which starts the Hello World MQTT demo\. Depending on the configuration you selected when you downloaded Amazon FreeRTOS, and depending on where you downloaded Amazon FreeRTOS, the other example runner functions are either commented out or omitted entirely\.

**Note**  
Be aware that not all combinations of examples work together\. Depending on the combination, the software might fail to execute on the selected target due to memory constraints\. We recommend that you run one demo at a time\.

## Configuring the Demos<a name="configuring-demos"></a>

The demos have been configured to get you started quickly\. You might want to change some of the configurations for your project to create a version that runs on your platform\. You can find configuration files at `demos/<vendor>/<platform>/common/config_files`\.
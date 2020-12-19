# AWS IoT Device Shadow library<a name="freertos-lib-cloud-shadows"></a>

## Introduction<a name="freertos-shadow-introduction"></a>

You can use the AWS IoT Device Shadow library to store and retrieve the current state \(the *shadow*\) of every registered device\. The device's shadow is a persistent, virtual representation of your device that you can interact with in your web applications even if the device is offline\. The device state is captured as its shadow in a [JSON](https://www.json.org/) document\. You can send commands to the AWS IoT Device Shadow service over MQTT or HTTP to query the latest known device state, or to change the state\. Each device's shadow is uniquely identified by the name of the corresponding *thing*, a representation of a specific device or logical entity on the AWS Cloud\. For more information, see [ Managing Devices with AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/iot-thing-management.html)\. More details about shadows can be found in [AWS IoT documentation](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\. 

The AWS IoT Device Shadow library has no dependencies on additional libraries other than the standard C library\. It also doesn't have any platform dependencies, such as threading or synchronization\. It can be used with any MQTT library and any JSON library\. 

This library can be freely used and is distributed under the [MIT open source license](https://freertos.org/a00114.html)\.

```
---------------------------------------------------------------------------
| Code Size of AWS IoT Device Shadow                                      |
|     (example generated with [GCC for ARM Cortex\-M](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads/9-2019-q4-major))                       |
|-------------------------------------------------------------------------|
| File                    | With -O1 Optimisation | With -Os Optimisation |
|-------------------------|-----------------------|-----------------------|
| shadow.c                | 1.2K                  | 0.7K                  |
|-------------------------|-----------------------|-----------------------|
| Total estimate          | 1.2K                  | 0.7K                  |
---------------------------------------------------------------------------
```
# AWS IoT Device SDK for Embedded C<a name="c-sdk"></a>

The AWS IoT Device SDK for Embedded C is a collection of C source files that can be used in embedded applications to securely connect to the AWS IoT platform\. The SDK includes transport clients, TLS implementations, and usage examples\. It also includes libraries that interact with AWS IoT services on the AWS Cloud\.

The AWS IoT Device SDK for Embedded C includes the following libraries\. These libraries have the same APIs as their corresponding FreeRTOS libraries\.
+ AWS IoT Device Defender
+ MQTT
+ AWS IoT Device Shadow
+ Common Libraries
  + Linear Containers
  + Logging
  + Static Memory
  + Task Pool

The SDK is distributed as source code that is intended to be built into customer firmware along with application code, other libraries, and RTOS\. For more information, see the [AWS IoT Device SDK for Embedded C GitHub](https://github.com/aws/aws-iot-device-sdk-embedded-C)\.

For instructions on porting the SDK source code to your environment, see the [AWS IoT Device SDK for Embedded C Porting Guide](https://github.com/aws/aws-iot-device-sdk-embedded-C/blob/master/PortingGuide.md)\.

For an API reference, see the [AWS IoT Device SDK C API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/index.html)\. 
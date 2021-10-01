# AWS IoT Device SDK for Embedded C<a name="c-sdk"></a>

**Note**  
This SDK is intended for use by experienced embedded\-software developers\.

The AWS IoT Device SDK for Embedded C \(C\-SDK\) is a collection of C source files under the MIT open source license that can be used in embedded applications to securely connect IoT devices to AWS IoT Core\. It includes an MQTT client, HTTP client, JSON Parser, and AWS IoT Device Shadow, AWS IoT Jobs and AWS IoT Device Defender libraries\. This SDK is distributed in source form and can be built into customer firmware along with application code, other libraries, and an operating system \(OS\) of your choice\.

For Fleet Provisioning, use the `v4_beta_deprecated` version of the AWS IoT Device SDK for Embedded C at [ https://github\.com/aws/aws\-iot\-device\-sdk\-embedded\-C/tree/v4\_beta\_deprecated](https://github.com/aws/aws-iot-device-sdk-embedded-C/tree/v4_beta_deprecated)\. Please review the README in this branch for more details\.

The AWS IoT Device SDK for Embedded C is generally targeted at resource constrained devices that require an optimized C language runtime\. You can use the SDK on any operating system and host it on any processor type \(for example, MCUs and MPUs\)\. However, if your devices have sufficient memory and processing resources, we recommend that you use one of the higher order [AWS IoT Device SDKs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-sdks.html#iot-device-sdks)\.

For more information, see the following:
+ [AWS IoT Device SDK for Embedded C](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/)
+ [AWS IoT Device SDK for Embedded C on GitHub](https://github.com/aws/aws-iot-device-sdk-embedded-C)
+ [AWS IoT Device SDK for Embedded C Readme](https://github.com/aws/aws-iot-device-sdk-embedded-C#aws-iot-device-sdk-for-embedded-c)
+ [ AWS IoT Device SDK for Embedded C Samples](https://docs.aws.amazon.com/embedded-csdk/202012.00/lib-ref/docs/doxygen/output/html/demos_main.html)
# AWS IoT Device Defender library<a name="afr-device-defender-library"></a>

## Introduction<a name="freertos-defender-introduction"></a>

You can use the AWS IoT Device Defender library to send security metrics from your IoT devices to AWS IoT Device Defender\. You can use AWS IoT Device Defender to continuously monitor these security metrics from devices for deviations from what you have defined as appropriate behavior for each device\. If something doesn’t look right, AWS IoT Device Defender sends out an alert so that you can take action to fix the issue\. Interactions with AWS IoT Device Defender use [MQTT](https://freertos.org/mqtt/index.html), a lightweight publish\-subscribe protocol\. This library provides an API to compose and recognize the MQTT topic strings used by AWS IoT Device Defender\.

 For more information, see [AWS IoT Device Defender](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender.html) in the *AWS IoT Developer Guide*\.

The library is written in C and designed to be compliant with [ISO C90](https://en.wikipedia.org/wiki/ANSI_C#C90) and [MISRA C:2012](https://www.misra.org.uk/MISRAHome/MISRAC2012/tabid/196/Default.aspx)\. The library has no dependencies on any additional libraries other than the standard C library\. It also doesn’t have any platform dependencies, such as threading or synchronization\. It can be used with any MQTT library and any [JSON](https://freertos.org/json/json-terminology.html) or [CBOR](https://cbor.io/) library\. The library has [proofs](https://www.cprover.org/cbmc/) showing safe memory use and no heap allocation, making it suitable for IoT microcontrollers, but also fully portable to other platforms\.

The AWS IoT Device Defender library can be freely used and is distributed under the [MIT open source license](https://freertos.org/a00114.html)\.


****  

| Code Size of AWS IoT Device Defender \(example generated with GCC for ARM Cortex\-M\) | File | With \-O1 Optimisation | With \-Os Optimisation | 
| --- | --- | --- | --- | 
| defender\.c | 1\.1K | 0\.6K | 
| Total estimate | 1\.1K | 0\.6K | 
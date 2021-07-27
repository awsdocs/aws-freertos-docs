# AWS IoT Jobs library<a name="freertos-lib-jobs"></a>

## Introduction<a name="freertos-lib-jobs-introduction"></a>

AWS IoT Jobs is a service that notifies one or more connected devices of a pending *job*\. You can use a job to manage your fleet of devices, update firmware and security certificates on your devices, or perform administrative tasks such as restarting devices and performing diagnostics\. For more information, see [Jobs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) in the *AWS IoT Developer Guide*\. Interactions with the AWS IoT Jobs service use [MQTT](https://freertos.org/mqtt/index.html), a lightweight publish\-subscribe protocol\. This library provides an API to compose and recognize the MQTT topic strings used by the AWS IoT Jobs service\.

The AWS IoT Jobs library is written in C and designed to be compliant with [ISO C90](https://en.wikipedia.org/wiki/ANSI_C#C90) and [MISRA C:2012](https://www.misra.org.uk/MISRAHome/MISRAC2012/tabid/196/Default.aspx)\. The library has no dependencies on any additional libraries other than the standard C library\. It can be used with any MQTT library and any JSON library\. The library has [proofs](https://www.cprover.org/cbmc/) showing safe memory use and no heap allocation, making it suitable for IoT microcontrollers, but also fully portable to other platforms\.

This library can be freely used and is distributed under the [MIT open source license](https://freertos.org/a00114.html)\.


****  

| Code Size of AWS IoT Jobs \(example generated with GCC for ARM Cortex\-M\) | File | With \-O1 Optimization | With \-Os Optimization | 
| --- | --- | --- | --- | 
| jobs\.c | 1\.8K | 1\.5K | 
| Total estimates | 1\.8K | 1\.5K | 
# Configuring the coreMQTT library<a name="afr-porting-mqtt"></a>

Devices on the edge can use the MQTT protocol to communicate with the AWS Cloud\. AWS IoT hosts an MQTT broker that sends and receives messages to and from connected devices at the edge\.

The coreMQTT library implements the MQTT protocol for devices running FreeRTOS\. The coreMQTT library doesn't need to be ported, but your device's test project must pass all MQTT tests for qualification\. For more information, see [coreMQTT Library](https://docs.aws.amazon.com/freertos/latest/userguide/coremqtt.html) in the *FreeRTOS User Guide\.*

## Prerequisites<a name="testing-prereqs-mqtt"></a>

To set up the coreMQTT library tests, you need a network transport interface port\. See [Porting the Network Transport Interface](afr-porting-network-transport-interface.md) to learn more\.

## Testing<a name="testing-mqtt"></a>

Run coreMQTT Integration tests:
+ Register your client certificate with MQTT broker\.
+ Set the broker endpoint in `config` and run the integration tests\.

## Create reference MQTT demo<a name="reference-mqtt"></a>

We recommend using the coreMQTT agent to handle thread safety for all MQTT operations\. The user will also need publish and subscribe tasks, and Device Advisor tests to validate if the application integrates TLS, MQTT and other FreeRTOS libraries effectively\. 

To officially qualify a device for FreeRTOS, validate your integration project with AWS IoT Device Tester MQTT test cases\. See [AWS IoT Device Advisor workflow](https://docs.aws.amazon.com/iot/latest/developerguide/device-advisor-console-tutorial.html) for instructions to set up and test\. Mandated test cases for TLS and MQTT are listed below:


**TLS Test Cases**  

| Test Case | Test cases | Required tests | 
| --- | --- | --- | 
| TLS | TLS Connect | Yes | 
| TLS | TLS Support AWS IoT Cipher Suites | A recommended [cipher suite](https://docs.aws.amazon.com/iot/latest/developerguide/transport-security.html#tls-cipher-suite-support)  | 
| TLS | TLS Unsecure Server Cert | Yes | 
| TLS | TLS Incorrect Subject Name Servr Cert | Yes | 


**MQTT Test Cases**  

| Test Case | Test cases | Required tests | 
| --- | --- | --- | 
| MQTT | MQTT Connect | Yes | 
| MQTT | MQTT Connect Jitter Retries | Yes | 
| MQTT | MQTT Connect Exponential Backoff Retries | Yes | 
| MQTT | MQTT Subscribe | Yes | 
| MQTT | MQTT Publish | Yes | 
| MQTT | MQTT ClientPuback QoS1 | Yes | 
| MQTT | MQTT No Ack PingResp | Yes | 
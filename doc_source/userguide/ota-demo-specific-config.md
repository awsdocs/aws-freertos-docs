# Over\-the\-air demo configurations<a name="ota-demo-specific-config"></a>

The OTA demo configurations are demo\-specific configuration options provided in `aws_iot_ota_update_demo.c`\. These configurations are different from the OTA library configurations provided in the OTA library config file\. 

**OTA\_DEMO\_KEEP\_ALIVE\_SECONDS**  
For the MQTT client, this configuration is the maximum time interval that can elapse between finishing the transmission of one control packet and starting to send the next\. In the absence of a control packet, a PINGREQ is sent\. The broker must disconnect a client that doesn't send a message or a PINGREQ packet in one and a half times of this keep alive interval\. This configuration should be adjusted based on the application's requirements\.

**OTA\_DEMO\_CONN\_RETRY\_BASE\_INTERVAL\_SECONDS**  
The base interval, in seconds, before retrying the network connection\. The OTA demo will try to reconnect after this base time interval\. The interval is doubled after every failed attempt\. A random delay, up to a maximum of this base delay, is also added to the interval\.

**OTA\_DEMO\_CONN\_RETRY\_MAX\_INTERVAL\_SECONDS**  
The maximum interval, in seconds, before retrying the network connection\. The reconnect delay is doubled on every failed attempt, but it can go only up to this maximum value, plus a jitter of the same interval\.
# OTA Update Manager Service<a name="ota-manager"></a>

The OTA Update Manager service provides a way to:
+ Create an OTA update\.
+ Get information about an OTA update\. 
+ List all OTA updates associated with your AWS account\.
+ Delete an OTA update\.

An OTA update is a data structure maintained by the OTA Update Manager service\. It contains:
+ An OTA update ID\.
+ An optional OTA update description\.
+ A list of devices to update \(*targets*\)\.
+ The type of OTA update: CONTINUOUS or SNAPSHOT\.
+ A list of files to send to the target devices\.
+ An IAM role that allows access to the AWS IoT Jobs service\.
+ An optional list of user\-defined name\-value pairs\.

OTA updates were designed to be used to update device firmware, but you can use them to send any files you want to one or more devices registered with AWS IoT\. When you send files over the air, it is best practice to digitally sign them so the devices that receive the files can verify they have not been tampered with en route\. You can sign your files with [Code Signing for Amazon FreeRTOS](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) or you can use your own code\-signing tools\.

After your files have been digitally signed, you use the Amazon Streaming service to create a stream\. The service breaks up your files into blocks that can be sent over MQTT to your devices\.

When you create an OTA update, the OTA Manager service creates an [AWS IoT job](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) to notify your devices an update is available\. The Amazon FreeRTOS OTA agent runs on your devices and listens for update messages\. When an update is available, it streams the update over MQTT and stores the files locally\. It checks the digital signature of the downloaded files and if valid, installs the firmware update\. If you are not using Amazon FreeRTOS, you must implement your own OTA agent to listen for and download updates and perform any installation operations\.
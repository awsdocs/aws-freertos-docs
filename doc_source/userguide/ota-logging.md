# Set Up Cloudwatch Logs for OTA Updates<a name="ota-logging"></a>

The OTA Update service supports logging with Amazon CloudWatch\. You can use the AWS IoT console to enable and configure Amazon CloudWatch logging for OTA updates\. For more information about CloudWatch Logs, see [Cloudwatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)\.

To enable logging, you must create an IAM role and configure OTA update logging\.

**Note**  
Before you enable OTA update logging, make sure you understand the CloudWatch Logs access permissions\. Users with access to CloudWatch Logs can see your debugging information\. For information, see [Authentication and Access Control for Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/auth-and-access-control-cwl.html)\.

## Create a Logging Role and Enable Logging<a name="create-ota-logging-role"></a>

Use the [AWS IoT console](https://console.aws.amazon.com/console/home) to create a logging role and enable logging\.

1. From the navigation pane, choose **Settings**\.

1. Under **Logs**, choose **Edit**\.

1. Under **Level of verbosity**, choose **Debug**\.

1. Under **Set role**, choose **Create new** to create an IAM role for logging\.

1. Under **Name**, enter a unique name for your role\. Your role will be created with all required permissions\.

1. Choose **Update**\.

## OTA Update Logs<a name="ota-logs"></a>

The OTA Update service publishes logs to your account when one of the following occurs:
+ An OTA update is created\.
+ An OTA update is completed\.
+ A code\-signing job is created\.
+ A code\-signing job is completed\.
+ An AWS IoT job is created\.
+ An AWS IoT job is completed\.
+ A stream is created\.

You can view your logs in the [CloudWatch console\.](https://console.aws.amazon.com/cloudwatch/home)

**To view an OTA update in CloudWatch Logs**

1. From the navigation pane, choose **Logs**\.

1. In **Log Groups**, choose **AWSIoTLogsV2**\.

OTA update logs can contain the following properties:

accountId  
The AWS account ID in which the log was generated\.

actionType  
The action that generated the log\. This can be set to one of the following values:  
+ `CreateOTAUpdate`: An OTA update was created\.
+ `DeleteOTAUpdate`: An OTA update was deleted\.
+ `StartCodeSigning`: A code\-signing job was started\.
+ `CreateAWSJob`: An AWS IoT job was created\.
+ `CreateStream`: A stream was created\.
+ `GetStream`: A request for a stream was sent to the AWS IoT Streaming service\.
+ `DescribeStream`: A request for information about a stream was sent to the AWS IoT Streaming service\.

awsJobId  
The AWS IoT job ID that generated the log\.

clientId  
The MQTT client ID that made the request that generated the log\.

clientToken  
The client token associated with the request that generated the log\.

details  
More information about the operation that generated the log\.

logLevel  
The logging level of the log\. For OTA update logs, this is always set to `DEBUG`\.

otaUpdateId  
The ID of the OTA update that generated the log\.

protocol  
The protocol used to make the request that generated the log\.

status  
The status of the operation that generated the log\. Valid values are:  
+ Success
+ Failure

streamId  
The AWS IoT stream ID that generated the log\.

timestamp  
The time when the log was generated\.

topicName  
An MQTT topic used to make the request that generated the log\.

### Example Logs<a name="ota-example-logs"></a>

The following is an example log generated when a code\-signing job is started:

```
{ 
    "timestamp": "2018-07-23 22:59:44.955", 
    "logLevel": "DEBUG", 
    "accountId": "875157236366", 
    "status": "Success", 
    "actionType": "StartCodeSigning", 
    "otaUpdateId": "08957b03-eea3-448a-87fe-743e6891ca3a", 
    "details": "Start code signing job. The request status is SUCCESS." 
}
```

The following is an example log generated when an AWS IoT job is created:

```
{ 
    "timestamp": "2018-07-23 22:59:45.363", 
    "logLevel": "DEBUG", 
    "accountId": "123456789012", 
    "status": "Success", 
    "actionType": "CreateAWSJob", 
    "otaUpdateId": "08957b03-eea3-448a-87fe-743e6891ca3a", 
    "awsJobId": "08957b03-eea3-448a-87fe-743e6891ca3a", 
    "details": "Create AWS Job The request status is SUCCESS." 
}
```

The following is an example log generated when an OTA update is created:

```
{ 
    "timestamp": "2018-07-23 22:59:45.413", 
    "logLevel": "DEBUG", 
    "accountId": "123456789012", 
    "status": "Success", 
    "actionType": "CreateOTAUpdate", 
    "otaUpdateId": "08957b03-eea3-448a-87fe-743e6891ca3a", 
    "details": "OTAUpdate creation complete. The request status is SUCCESS." 
}
```

The following is an example log generated when a stream is created:

```
{ 
	"timestamp": "2018-07-23 23:00:26.391", 
	"logLevel": "DEBUG", 
	"accountId": "123456789012", 
	"status": "Success", 
	"actionType": "CreateStream", 
	"otaUpdateId": "3d3dc5f7-3d6d-47ac-9252-45821ac7cfb0", 
	"streamId": "6be2303d-3637-48f0-ace9-0b87b1b9a824", 
	"details": "Create stream. The request status is SUCCESS." 
}
```

The following is an example log generated when an OTA update is deleted:

```
{ 
	"timestamp": "2018-07-23 23:03:09.505", 
	"logLevel": "DEBUG", 
	"accountId": "123456789012", 
	"status": "Success", 
	"actionType": "DeleteOTAUpdate", 
	"otaUpdateId": "9bdd78fb-f113-4001-9675-1b595982292f", 
	"details": "Delete OTA Update. The request status is SUCCESS." 
}
```

The following is an example log generated when a device requests a stream from the streaming service:

```
{ 
	"timestamp": "2018-07-25 22:09:02.678", 
	"logLevel": "DEBUG", 
	"accountId": "123456789012", 
	"status": "Success", 
	"actionType": "GetStream", 
	"protocol": "MQTT", 
	"clientId": "b9d2e49c-94fe-4ed1-9b07-286afed7e4c8", 
	"topicName": "$aws/things/b9d2e49c-94fe-4ed1-9b07-286afed7e4c8/streams/1e51e9a8-9a4c-4c50-b005-d38452a956af/get/json", 
	"streamId": "1e51e9a8-9a4c-4c50-b005-d38452a956af", 
	"details": "The request status is SUCCESS." 
}
```

The following is an example log generated when a device calls the `DescribeStream` API:

```
{ 
	"timestamp": "2018-07-25 22:10:12.690", 
	"logLevel": "DEBUG", 
	"accountId": "123456789012", 
	"status": "Success", 
	"actionType": "DescribeStream", 
	"protocol": "MQTT", 
	"clientId": "581075e0-4639-48ee-8b94-2cf304168e43", 
	"topicName": "$aws/things/581075e0-4639-48ee-8b94-2cf304168e43/streams/71c101a8-bcc5-4929-9fe2-af563af0c139/describe/json", 
	"streamId": "71c101a8-bcc5-4929-9fe2-af563af0c139", 
	"clientToken": "clientToken", 
	"details": "The request status is SUCCESS." 
}
```
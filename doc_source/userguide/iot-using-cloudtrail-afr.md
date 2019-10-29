# Log AWS IoT OTA API Calls with AWS CloudTrail<a name="iot-using-cloudtrail-afr"></a>

Amazon FreeRTOS is integrated with CloudTrail, a service that captures AWS IoT OTA API calls and delivers the log files to an Amazon S3 bucket that you specify\. CloudTrail captures API calls from your code to the AWS IoT OTA APIs\. Using the information collected by CloudTrail, you can determine the request that was made to AWS IoT OTA, the source IP address from which the request was made, who made the request, when it was made, and so on\. 

For more information about CloudTrail, including how to configure and enable it, see the [https://docs.aws.amazon.com/awscloudtrail/latest/userguide/](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Amazon FreeRTOS Information in CloudTrail<a name="aws-iot-info-in-cloudtrail-afr"></a>

When CloudTrail logging is enabled in your AWS account, API calls made to AWS IoT OTA actions are tracked in CloudTrail log files where they are written with other AWS service records\. CloudTrail determines when to create and write to a new file based on a time period and file size\.

The following AWS IoT OTA control plane actions are logged by CloudTrail:
+ [CreateStream](https://docs.aws.amazon.com/iot/latest/apireference/API_CreateStream.html)
+ [DescribeStream](https://docs.aws.amazon.com/iot/latest/apireference/API_DescribeStream.html)
+ [ListStreams](https://docs.aws.amazon.com/iot/latest/apireference/API_ListStreams.html)
+ [UpdateStream](https://docs.aws.amazon.com/iot/latest/apireference/API_UpdateStream.html)
+ [DeleteStream](https://docs.aws.amazon.com/iot/latest/apireference/API_DeleteStream.html)
+ [CreateOTAUpdate](https://docs.aws.amazon.com/iot/latest/apireference/API_CreateOTAUpdate.html)
+ [GetOTAUpdate](https://docs.aws.amazon.com/iot/latest/apireference/API_GetOTAUpdate.html)
+ [ListOTAUpdates](https://docs.aws.amazon.com/iot/latest/apireference/API_ListOTAUpdates.html)
+ [DeleteOTAUpdate](https://docs.aws.amazon.com/iot/latest/apireference/API_DeleteOTAUpdate.html)

**Note**  
AWS IoT OTA data plane actions \(device side\) are not logged by CloudTrail\. Use CloudWatch to monitor these\.

Every log entry contains information about who generated the request\. The user identity information in the log entry helps you determine the following: 
+ Whether the request was made with root or IAM user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\. AWS IoT OTA actions are documented in the [AWS IoT OTA API Reference](https://docs.aws.amazon.com/iot/latest/apireference/)\.

You can store your log files in your Amazon S3 bucket for as long as you want, but you can also define Amazon S3 lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted with Amazon S3 server\-side encryption \(SSE\)\.

If you want to be notified when log files are delivered, you can configure CloudTrail to publish Amazon SNS notifications\. For more information, see [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

You can also aggregate AWS IoT OTA log files from multiple AWS Regions and multiple AWS accounts into a single Amazon S3 bucket\. 

For more information, see [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)\.

## Understanding Amazon FreeRTOS Log File Entries<a name="understanding-aws-iot-entries-afr"></a>

CloudTrail log files can contain one or more log entries\. Each entry lists multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. Log entries are not an ordered stack trace of the public API calls, so they do not appear in any specific order\. 

The following example shows a CloudTrail log entry that demonstrates the log from a call to `CreateOTAUpdate` action\.

```
{
	"eventVersion": "1.05",
	"userIdentity": {
		"type": "IAMUser",
		"principalId": "EXAMPLE",
		"arn": "arn:aws:iam::<your_aws_account>:user/<your_user_id>",
		"accountId": "<your_aws_account>",
		"accessKeyId": "<your_access_key_id>",
		"userName": "<your_username>",
		"sessionContext": {
			"attributes": {
				"mfaAuthenticated": "false",
				"creationDate": "2018-08-23T17:27:08Z"
			}
		},
		"invokedBy": "apigateway.amazonaws.com"
	},
	"eventTime": "2018-08-23T17:27:19Z",
	"eventSource": "iot.amazonaws.com",
	"eventName": "CreateOTAUpdate",
	"awsRegion": "<your_aws_region>",
	"sourceIPAddress": "apigateway.amazonaws.com",
	"userAgent": "apigateway.amazonaws.com",
	"requestParameters": {
		"targets": [
			"arn:aws:iot:<your_aws_region>:<your_aws_account>:thing/Thing_CMH"
		],
		"roleArn": "arn:aws:iam::<your_aws_account>:role/Role_FreeRTOSJob",
		"files": [
			{
				"fileName": "/sys/mcuflashimg.bin",
				"fileSource": {
					"fileId": 0,
					"streamId": "<your_stream_id>"
				},
				"codeSigning": {
					"awsSignerJobId": "<your_signer_job_id>"
				}
			}
		],
		"targetSelection": "SNAPSHOT",
		"otaUpdateId": "FreeRTOSJob_CMH-23-1535045232806-92"
	},
	"responseElements": {
		"otaUpdateArn": "arn:aws:iot:<your_aws_region>:<your_aws_account>:otaupdate/FreeRTOSJob_CMH-23-1535045232806-92",
		"otaUpdateStatus": "CREATE_PENDING",
		"otaUpdateId": "FreeRTOSJob_CMH-23-1535045232806-92"
	},
	"requestID": "c9649630-a6f9-11e8-8f9c-e1cf2d0c9d8e",
	"eventID": "ce9bf4d9-5770-4cee-acf4-0e5649b845c0",
	"eventType": "AwsApiCall",
	"recipientAccountId": "<recipient_aws_account>"
}
```
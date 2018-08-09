# Amazon FreeRTOS Over\-the\-Air Updates<a name="freertos-ota-dev"></a>

The Amazon FreeRTOS Over\-the\-Air Update service enables you to:
+ Digitally sign and encrypt firmware before deployment\.
+ Securely deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, reset, or reprovisioned\.
+ Verify the authenticity and integrity of new firmware after it's deployed to devices\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.

The Amazon FreeRTOS OTA Update service includes the following components:

OTA Manager Service  
The OTA Manager service enables users to create and manage deployments of firmware images on one or more devices or MCUs\. The OTA Manager Service uses the AWS IoT Jobs service to schedule deployments\.

AWS IoT Jobs Service  
The AWS IoT Jobs service is a cloud\-based managed service for scheduling, orchestration, notification, and status reporting of OTA updates and other remote operations on distributed fleets of devices\. To update a device, you create an OTA update job\. The job specifies which devices should perform the update and where to find the firmware image, among other things\. When a job is deployed to a device, a job execution is created\. The job execution represents a single device applying the update\. For more information, see [AWS IoT Jobs](http://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html)\.

Code Signing for Amazon FreeRTOS Service  
Code Signing for Amazon FreeRTOS is a managed AWS service that enables you to digitally sign code that you create for any AWS IoT device that is supported by Amazon Web Services \(AWS\)\. Code Signing is integrated with Amazon FreeRTOS and AWS Certificate Manager \(ACM\)\. Amazon FreeRTOS customers can use Code Signing for Amazon FreeRTOS to sign a firmware image before publishing it to a MCU device\. You can use ACM to import a third\-party code signing certificate that you can use during the signing process\.

Streaming Service  
The Streaming service delivers new firmware images over MQTT to your devices\. The Streaming service breaks up the firmware image into chunks and delivers each chunk as an MQTT message to the devices that are being updated\. The service can redeliver blocks or a full image on request\.

OTA Library and Agent  
The OTA library allows the device developer to logically separate the application from the OTA process\. The OTA library contains an OTA agent that is executed as an RTOS task\.   
The OTA agent is responsible for:  
+ Downloading a new firmware image from the cloud\.
+ Validating the firmware image\.
+ Handling interruptions during the download\.
+ Managing updates that are separated into multiple sections\. 
By automating firmware signature verification, the OTA library makes it easy for you to protect the integrity of your devices\. By defining a portable abstraction layer \(PAL\), the OTA library minimizes the burden for on boarding new hardware to OTA\-enabled applications\. 

OTA is supported in the following regions:
+ `us-east-1` / US East \(N\. Virginia\)
+ `us-east-2` / US East \(Ohio\)
+ `us-west-2` / US West \(Oregon\)
+ `eu-west-1` / EU \(Ireland\)
+ `eu-central-1` / EU \(Frankfurt\)
+ `eu-west-2` / EU \(London\)
+ `ap-northeast-1` / Asia Pacific \(Tokyo\)
+ `ap-southeast-2` / Asia Pacific \(Sydney\)

The region you are working in is displayed in the upper\-right corner of the AWS Management Console\. You can use the drop\-down list to change the region\. Before you create an OTA update, make sure you are working in one of these supported regions\.

**Note**  
For the Amazon FreeRTOS OTA agent to commit a firmware upgrade, the firmware image must include the OTA agent library\. The firmware version must be more recent than the currently installed firmware\.

## OTA Update Manager Service<a name="ota-manager"></a>

The OTA update manager service enables you to create and manage OTA updates\. It provides a way to create an OTA update, get information about an existing OTA update, list all OTA updates associated with your AWS account, and delete an OTA update\.

An OTA update is a data structure maintained by the OTA update manager service\. It contains:
+ An OTA update ID\.
+ An optional OTA update description\.
+ A list of devices to update \(targets\)\.
+ The type of OTA update: CONTINUOUS or SNAPSHOT\.
+ A list of files to send to the target devices\.
+ An IAM role that allows access to the AWS IoT Jobs service\.
+ An optional list of user\-defined name/value pairs\.

OTA updates were designed to be used to update device firmware, but you can use it to send any files you want to one or more devices registered with AWS IoT\. When sending files over\-the\-air, it is best practice to digitally sign them so the devices that receive the files can verify they have not been tampered with en\-route\. You can use the [Code Signing for Amazon FreeRTOS Service](#code-signing) to sign your files or you can sign your files with your own code signing tools\.

Once your files have been digitally signed, you create a stream using the Amazon Streaming service which breaks up your files into blocks that can be sent over MQTT to your devices\.

When you create an OTA update, the OTA Manager service creates an [AWS IoT Job](http://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) to notify your devices an update is available\. The Amazon FreeRTOS OTA Agent runs on your devices and listens for update messages\. When an update is available, it streams the update over MQTT and stores the files locally\. It checks the digital signature of the downloaded files and if valid, installs the firmware update\. If you are not using Amazon FreeRTOS, you will have to implement your own OTA agent to listen for, download updates, and perform any installation operations\.

## AWS IoT Jobs Service<a name="ota-jobs"></a>

AWS IoT jobs can be used to define a set of remote operations that are sent to and executed on one or more devices connected to AWS IoT\. For more information, see [AWS IoT Jobs](http://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html)\.

## Code Signing for Amazon FreeRTOS Service<a name="code-signing"></a>

Code Signing for Amazon FreeRTOS is a service that automatically digitally signs firmware \(or other files\) for distribution over OTA updates\. Code Signing for Amazon FreeRTOS is integrated with AWS Certificate Manager and uses certificates registered in ACM to sign your files\. For more information, see [Code Signing for Amazon FreeRTOS](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.

## OTA Streaming Service<a name="ota-stream"></a>

The OTA Streaming service creates a stream for delivering one or more large files in blocks over MQTT\. You can have one or more files associated with a stream\. The total size of the files associated with a stream cannot exceed 2 MB\. The Streaming service provides two interfaces: An HTTP/CLI interface for stream management and an MQTT interface for streaming data\.

### Stream Management<a name="stream-control-plane"></a>

Stream management API are available as part of the AWS HTTP API and the CLI\. For each HTTP API there is a corresponding CLI command\.

#### CreateStream<a name="create-stream"></a>

The [CreateStream](http://docs.aws.amazon.com/iot/latest/apireference/API_CreateStream.html) API creates a stream\. To call this API use the [create\-stream](https://docs.aws.amazon.com/cli/latest/reference/iot/create-stream.html) CLI command\. It takes the following parameters:

stream\-id  
A string containing the user defined stream ID\.

description  
An optional description of the string\.

files  
A JSON file that contains a list of files to stream\.

role\-arn  
An IAM role that allows the AWS IoT service to access your Amazon S3 files\.

The JSON file that is passed in the `files` parameter \(stream\.json in the example below\) contains information about each file to stream including a file ID and the file's location in Amazon S3\. The following is an example of such a file:

```
[
    {
        "fileId":123,
        "s3Location": {
            "bucket":"ota-bucket",
            "key":"1af68405-b481-4dc5-b2f8-4e12e1515649"
        }
    }
]
```

Here is an example call to `create-stream` and its output:

```
$ aws iot create-stream --stream-id 111 --description "an OTA stream" --files file://stream.json
						--role-arn arn:aws:iam::123456789012:role/OTA_SERVICE_ROLE
{
    "streamId": "111",
    "streamArn": "arn:aws:iot:us-west-2:123456789012:stream/111",
    "description": "an OTA stream",
    "streamVersion": 1
}
```

#### DescribeStream<a name="describe-stream"></a>

The [DescribeStream](http://docs.aws.amazon.com/iot/latest/apireference/API_DescribeStream.html) API displays information about the specified stream\. To call this API use the [describe\-stream](https://docs.aws.amazon.com/cli/latest/reference/iot/describe-stream.html) CLI command\. It takes the following parameters:

stream\-id  
The ID of the stream to describe\.

Here is an example call to `describe-stream` and its output:

```
aws iot describe-stream --stream-id 111
{
    "streamInfo": {
        "streamId": "111",
        "streamArn": "arn:aws:iot:us-west-2:123456789012:stream/111",
        "streamVersion": 1,
        "description": "an OTA stream",
        "files": [
            {
                "fileId": 123,
                "s3Location": {
                    "bucket": "ota-bucket",
                    "key": "1af68405-b481-4dc5-b2f8-4e12e1515649"
                }
            }
        ],
        "createdAt": 1529535495.523,
        "lastUpdatedAt": 1529535495.523,
        "roleArn": "arn:aws:iam::123456789012:role/OTA_SERVICE_ROLE"
    }
}
```

#### ListStreams<a name="list-streams"></a>

The [ListStreams](http://docs.aws.amazon.com/iot/latest/apireference/API_ListStreams.html) API lists all streams associated with your AWS account\. To call this API use the [list\-streams](https://docs.aws.amazon.com/cli/latest/reference/iot/list-streams.html) CLI command\.

Here is an example call to `list-streams` and its output:

```
aws iot list-streams
{
    "streams": [
        {
            "streamId": "112",
            "streamArn": "arn:aws:iot:us-west-2:123456789012:stream/112",
            "streamVersion": 1
        },
        {
            "streamId": "111",
            "streamArn": "arn:aws:iot:us-west-2:123456789012:stream/111",
            "streamVersion": 1
        },
        {
            "streamId": "001",
            "streamArn": "arn:aws:iot:us-west-2:123456789012:stream/001",
            "streamVersion": 1
        }
    ]
}
```

#### DeleteStream<a name="delete-stream"></a>

The [DeleteStream](http://docs.aws.amazon.com/iot/latest/apireference/API_DeleteStream.html) API deletes a stream\. To call this API use the [delete\-stream](https://docs.aws.amazon.com/cli/latest/reference/iot/delete-stream.html) CLI command\. It takes the following parameters:

stream\-id  
The ID of the stream to delete\.

Here is an example calling `delete-stream`, it displays no output:

```
aws iot delete-stream --stream-id 001
```

### Streaming Data with the Streaming Service<a name="stream-data-plane"></a>

The Streaming service provides an MQTT API for devices to retrieve information about a stream and to stream files to devices\. Devices or applications subscribe and publish to MQTT topics to request blocks of streamed files\.

#### DescribeStream<a name="describe-stream-mqtt"></a>

To retrieve a list of files and their sizes associated with a stream, use the DescribeStream MQTT API\. To call this API, publish a message to the  `$aws/things/<thingName>/streams/<streamID>/describe` MQTT topic, where:

thingName  
Is the IoT thing name of the device that is streaming the data\.

streamID  
Is the ID of the stream you are using to stream data\.

The message sent should specify these attributes:

Message version \(v\)  
The version of the message\. This number is incremented every time the format of the message changes\. Supports backward compatibility and versioning\.

Client token \(t\)  
An optional client token\. The reply to a request includes the matching clientToken to allow you to correlate a request with its reply\. 

The following is an example message:

```
{
	  "v" : 001, 
	  "t" : "myClientToken"
	}
```

The Streaming service will respond by publishing a message to the  `$aws/things/<thingName>/streams/<streamID>` MQTT topic if the request is valid\. The message will contain the following attributes:

Message version \(v\)  
The version of the message\. This number is incremented every time the format of the message changes\. Supports backward compatibility and versioning\.

Client token \(t\)  
An optional client token\. The reply to a request includes the matching clientToken to allow you to correlate a request with its reply\. 

Expected stream version \(s\) \- optional  
The expected version of the stream\. The version is incremented every time a stream is updated/deleted/created\. If the device sends the expected version, the Streaming service will compare the expected stream version with the current version of the stream\. If there is a mismatch it will return a version mismatch error/rejected message\. If the version is not sent in the request message, the version check is skipped on the server side\.

Stream description \(d\)  
A text description of the stream\. This is specified when you create the stream using the `CreateStream` API\.

List of files \(r\)  
A list of files associated with the stream\. For each file associated with the stream the following attributes are present:    
Block index \(f\)  
The index of the current file block with in the stream\.  
File size \(z\)  
The size of the file being streamed\.

The following is an example response from the DescribeStream MQTT API:

```
{
	  "v" : 1, 
	  "t": "myClientToken", 
	  "s" : 1, 
	  "d" : number, // stream description
	  "r": [ 
	    {
	      "f": 001, 
	      "z": 10 
	    },
	    {
	      "f": 002, 
	      "z": 25 
	    }
	  ]
	}
```

If the request is not valid the Streaming service will publish a message to the `$aws/things/<thingName>/streams/<streamID>/describe/json/rejected` MQTT topic\. The following is an example response when an invalid request is made:

```
{
	  "code" : "InvalidTopic",
	  "message" : "$aws/things/<thingName>/streams/<streamID>/describe/json is not a recognized stream topic"
	}
```

#### GetStream<a name="get-stream-mqtt"></a>

To retrieve data from a stream, use the `GetStream` MQTT API\. To call this API, publish a message to the  `$aws/things/<thingName>/streams/<streamID>/get` MQTT topic, where:

thingName  
Is the IoT thing name of the device that is streaming the data\.

streamID  
Is the ID of the stream you are using to stream data\.

The message sent should specify these attributes:

Message version \(v\)  
The version of the message\. This number is incremented every time the format of the message changes\. Supports backward compatibility and versioning\.

Client token \(t\)  
Optional \- A client token\. The reply to a request includes the matching clientToken to allow you to correlate a request with its reply\. 

Expected stream version \(s\)  
Optional \- The expected version of the stream\.

File ID \(f\)  
The ID of the file being streamed\.

Block size \(l\)  
The block size\.

Block offset \(o\)  
The block offset\.

Number of blocks \(n\)  
The number of blocks to send\.

Block bitmap  
A bitmap that describes the blocks being requested\. Each bit in the bitmap corresponds to a block in the stream\. The length of the bitmap depends on the number of blocks in the stream, rounded up to the nearest byte\.

The streaming service breaks files up into chunks or blocks and uses a bitmap to request specific blocks\. Each block is identified by a block number\. Block numbers are calculated by bit position, byte offset and base offset as shown in the following diagram\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/bit-map.png)

The following is an example message sent with the GetStream MQTT API\. 

```
{
    "v" : number, // message version
    "t": "string", // client token (optional)
    "s" : number, // expected stream version (optional)
    "f" : number, // file ID
    "l" : number , // the block size
    "o" : number,  // block offset
    "n" : number, // the no of blocks to send
    "b" : binary // the block bitmap
}
```

## OTA agent<a name="ota-agent"></a>

The OTA agent runs on your devices and responds when an OTA update is available\. It then downloads the update over MQTT by making calls to the Streaming service, reassembles the update chunks and then installs the new update\. The [OTA Update Demo Application](http://docs.aws.amazon.com/freertos/latest/userguide/ota-demo.html) is an example application that uses the OTA agent to download an OTA update\. The source code for the OTA agent can be found in `<BASE_FOLDER>/lib/ota/aws_ota_agent.c`\. 

## Monitoring Updates<a name="monitoring-updates"></a>

The code deployment manager `createDeployment` API returns the job ID from the AWS IoT Jobs service\. You can use the job ID and the MQTT AWS IoT Jobs APIs to track the progress and status of the OTA updates across the fleet of the devices\.

`DescribeJobExecution`  
 Gets the details of a job execution\. A job execution is an instance of a job running on a single device\.

`ListJobExecutionsForJob`  
Lists all job executions for a job\.

`ListJobExecutionsForThing`  
Gets the list of all job executions for a thing\. 

For more information, see [ AWS IoT Jobs API](http://docs.aws.amazon.com/iot/latest/developerguide/jobs-api.html)\.

You can also connect a terminal emulator like [Tera Term](https://ttssh2.osdn.jp/index.html.en) or [CoolTerm](http://freeware.the-meiers.org/) to see serial output from your device as it processes an OTA update\.

## OTA Error Codes<a name="ota-errors"></a>

The following table lists error codes returned by the OTA Update service\.


****  

| Error Code | Description | 
| --- | --- | 
| ResourceAlreadyExist | The requested resource already exists in the AWS account\. | 
| ResourceNotFound | The requested resource does not exist\. | 
| InvalidRequest | The request is not valid\. | 
| VersionConflict | The requested resource is being updated\. | 
| Unauthorized | Access is denied\. | 
| StreamingFileConflict | The file associated with the stream has been modified\. | 
| InternalFailure | The service encountered an internal error\. | 

## OTA Security<a name="dev-guide-ota-security"></a>

The following are three aspects of OTA security:

Connection security  
The OTA Update service relies on existing security mechanisms like TLS mutual authentication, used by AWS IoT\. OTA update traffic passes through the AWS IoT device gateway and uses AWS IoT security mechanisms\. Each incoming and outgoing MQTT message through the device gateway undergoes strict authentication and authorization\.

Authenticity and integrity of OTA updates  
Firmware can be digitally signed before an OTA update to ensure that it is from a reliable source and has not been tampered with\. The Amazon FreeRTOS OTA Update service uses the Code Signing for Amazon FreeRTOS service to automatically sign your firmware\. For more information, see [Code Signing for Amazon FreeRTOS](http://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\. The OTA agent, which runs on your devices, performs integrity checks on the firmware when it arrives on the device\.

Operator security  
Every API call made through the control plane API undergoes standard IAM Signature Version 4 authentication and authorization\. To create a deployment, you must have permissions to invoke the `CreateDeployment`, `CreateJob`, and `CreateStream` APIs\. In addition, in your Amazon S3 bucket policy or ACL, you must give read permissions to the AWS IoT service principal so that the firmware update stored in Amazon S3 can be accessed during streaming\. 

### Code Signing for Amazon FreeRTOS<a name="dev-guide-code-signing"></a>

The AWS IoT console uses [Code Signing for Amazon FreeRTOS](http://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) to automatically sign your firmware image for any device supported by AWS IoT\.

Code Signing for Amazon FreeRTOS uses a certificate and private key that you import into ACM\. You can use a self–signed certificate for testing, but we recommend that you obtain a certificate from a well–known commercial certificate authority \(CA\)\.

Code–signing certificates use the X\.509 version 3 **Key Usage** and **Extended Key Usage** extensions\. The **Key Usage** extension is set to `Digital Signature` and the **Extended Key Usage** extension is set to `Code Signing`\. For more information about signing your code image, see the [Code Signing for Amazon FreeRTOS Developer Guide](http://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) and the [Code Signing for Amazon FreeRTOS API Reference](http://docs.aws.amazon.com/signer/latest/api/Welcome.html)\.

**Note**  
The Code Signing for Amazon FreeRTOS feature is currently in beta\. You can download the SDK from [https://tools\.signer\.aws\.a2z\.com/awssigner\-tools\.zip](https://tools.signer.aws.a2z.com/awssigner-tools.zip)\. 
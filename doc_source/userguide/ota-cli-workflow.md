# Creating an OTA update with the AWS CLI<a name="ota-cli-workflow"></a>

When you use the AWS CLI to create an OTA update, you:

1. Digitally sign your firmware image\.

1. Create a stream of your digitally signed firmware image\.

1. Start an OTA update job\.

## Digitally signing your firmware update<a name="ota-sign-cli"></a>

When you use the AWS CLI to perform OTA updates, you can use Code Signing for AWS IoT, or you can sign your firmware update yourself\. For a list of the cryptographic signing and hashing algorithms supported by Code Signing for AWS IoT, see [ SigningConfigurationOverrides](https://docs.aws.amazon.com/signer/latest/api/API_SigningConfigurationOverrides.html)\. If you want to use a cryptographic algorithm that is not supported by Code Signing for AWS IoT, you must sign your firmware binary before you upload it to Amazon S3\.

### Signing your firmware image with Code Signing for AWS IoT<a name="ota-sign-csfa"></a>

To sign your firmware image using Code Signing for AWS IoT, you can use one of the [AWS SDKs or command line tools](https://aws.amazon.com/tools/)\. For more information about Code Signing for AWS IoT, see [ Code Signing for AWS IoT](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.

After you install and configure the code\-signing tools, copy your unsigned firmware image to your Amazon S3 bucket and start a code\-signing job with the following CLI commands\. The put\-signing\-profile command creates a reusable code\-signing profile\. The start\-signing\-job command starts the signing job\.

```
aws signer put-signing-profile \
    --profile-name your_profile_name \
    --signing-material certificateArn=arn:aws:acm::your-region:your-aws-account-id:certificate/your-certificate-id \
    --platform your-hardware-platform \
    --signing-parameters certname=your_certificate_path_on_device
```

```
aws signer start-signing-job \
    --source 's3={bucketName=your_s3_bucket,key=your_s3_object_key,version=your_s3_object_version_id}' \
    --destination 's3={bucketName=your_destination_bucket}' \
    --profile-name your_profile_name
```

**Note**  
*your\-source\-bucket\-name* and *your\-destination\-bucket\-name* can be the same Amazon S3 bucket\.

These are the parameters for the put\-signing\-profile and start\-signing\-job commands:

`source`  
Specifies the location of the unsigned firmware in an S3 bucket\.  
+ `bucketName`: The name of your S3 bucket\.
+ `key`: The key \(file name\) of your firmware in your S3 bucket\.
+ `version`: The S3 version of your firmware in your S3 bucket\. This is different from your firmware version\. You can find it by browsing to the Amazon S3 console, choosing your bucket, and at the top of the page, next to **Versions**, choosing **Show**\.

`destination`  
The destination on the device to which the signed firmware in the S3 bucket will be copied\. The format of this parameter is the same as the `source` parameter\.

`signing-material`  
The ARN of your code\-signing certificate\. This ARN is generated when you import your certificate into ACM\.

`signing-parameters`  
A map of key\-value pairs for signing\. These can include any information that you want to use during signing\.  
This parameter is required when you are creating a code\-signing profile for signing OTA updates with Code Signing for AWS IoT\.

`platform`  
The `platformId` of the hardware platform to which you are distributing the OTA update\.  
To return a list of the available platforms and their `platformId` values, use the aws signer list\-signing\-platforms command\.

The signing job starts and writes the signed firmware image into the destination Amazon S3 bucket\. The file name for the signed firmware image is a GUID\. You need this file name when you create a stream\. You can find the file name by browsing to the Amazon S3 console and choosing your bucket\. If you don't see a file with a GUID file name, refresh your browser\.

The command displays a job ARN and job ID\. You need these values later on\. For more information about Code Signing for AWS IoT, see [Code Signing for AWS IoT](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.

### Signing your firmware image manually<a name="ota-sign-manual"></a>

Digitally sign your firmware image and upload your signed firmware image into your Amazon S3 bucket\.

## Creating a stream of your firmware update<a name="ota-stream"></a>

A stream is an abstract interface to data that can be consumed by a device\. A stream can hide the complexity of accessing data stored in different locations or different cloud\-based services\. The OTA Update Manager service enables you to use multiple pieces of data, stored in various locations in Amazon S3, to perform an OTA Update\.

When you create an AWS IoT OTA Update, you can also create a stream that contains your signed firmware update\. Make a JSON file \(`stream.json`\) that identifies your signed firmware image\. The JSON file should contain the following\.

```
[
  {
    "fileId":"your_file_id",
    "s3Location":{
      "bucket":"your_bucket_name",
      "key":"your_s3_object_key"
    }
  }   
]
```

These are the attributes in the JSON file:

`fileId`  
An arbitrary integer between 0–255 that identifies your firmware image\.

`s3Location`  
The bucket and key for the firmware to stream\.    
`bucket`  
The Amazon S3 bucket where your unsigned firmware image is stored\.  
`key`  
The file name of your signed firmware image in the Amazon S3 bucket\. You can find this value in the Amazon S3 console by looking at the contents of your bucket\.  
If you are using Code Signing for AWS IoT, the file name is a GUID generated by Code Signing for AWS IoT\.

Use the create\-stream CLI command to create a stream\.

```
aws iot create-stream \
    --stream-id your_stream_id \
    --description your_description \
    --files file://stream.json \
    --role-arn your_role_arn
```

These are the arguments for the create\-stream CLI command:

`stream-id`  
An arbitrary string to identify the stream\.

`description`  
An optional description of the stream\.

`files`  
One or more references to JSON files that contain data about firmware images to stream\. The JSON file must contain the following attributes:    
`fileId`  
An arbitrary file ID\.  
`s3Location`  
The bucket name where the signed firmware image is stored and the key \(file name\) of the signed firmware image\.  
`bucket`  
The Amazon S3 bucket where the signed firmware image is stored\.  
`key`  
The key \(file name\) of the signed firmware image\.  
When you use Code Signing for AWS IoT, this key is a GUID\.
The following is an example `stream.json` file\.  

```
[
    {
        "fileId":123,
        "s3Location": {
            "bucket":"codesign-ota-bucket",
            "key":"48c67f3c-63bb-4f92-a98a-4ee0fbc2bef6"
        }
    }
]
```

`role-arn`  
The [OTA service role](create-service-role.md) that also grants access to the Amazon S3 bucket where the firmware image is stored\.

To find the Amazon S3 object key of your signed firmware image, use the aws signer describe\-signing\-job \-\-job\-id *my\-job\-id* command where `my-job-id` is the job ID displayed by the create\-signing\-job CLI command\. The output of the describe\-signing\-job command contains the key of the signed firmware image\. 

```
... text deleted for brevity ...
  "signedObject": {
    "s3": {
      "bucketName": "ota-bucket",
      "key": "7309da2c-9111-48ac-8ee4-5a4262af4429"
    }
  }
... text deleted for brevity ...
```

**Note**  
If you see `"Error: You have exceeded the limit for the number of streams in your AWS account."`, then see [Stream limit exceeded for your AWS account](streaming-service.md#ota-troubleshooting-stream-limit)\.

## Creating an OTA update<a name="create-ota-update"></a>

Use the create\-ota\-update CLI command to create an OTA update job\.

**Note**  
Do not use any personally identifiable information \(PII\) in your OTA update job ID\. Examples of personally identifiable information include:  
Names\.
IP addresses\.
Email addresses\.
Locations\.
Bank details\.
Medical information\.

```
aws iot  create-ota-update \
    --ota-update-id value \
    [--description value] \
    --targets value \
    [--protocols value] \
    [--target-selection value] \
    [--aws-job-executions-rollout-config value] \
    [--aws-job-presigned-url-config value] \
    [--aws-job-abort-config value] \
    [--aws-job-timeout-config value] \
    --files value \
    --role-arn value \
    [--additional-parameters value] \
    [--tags value]  \
    [--cli-input-json value] \
    [--generate-cli-skeleton]
```

 `cli-input-json` format

```
{
  "otaUpdateId": "string",
  "description": "string",
  "targets": [
    "string"
  ],
  "protocols": [
    "string"
  ],
  "targetSelection": "string",
  "awsJobExecutionsRolloutConfig": {
    "maximumPerMinute": "integer",
    "exponentialRate": {
      "baseRatePerMinute": "integer",
      "incrementFactor": "double",
      "rateIncreaseCriteria": {
        "numberOfNotifiedThings": "integer",
        "numberOfSucceededThings": "integer"
      }
    }
  },
  "awsJobPresignedUrlConfig": {
    "expiresInSec": "long"
  },
  "awsJobAbortConfig": {
    "abortCriteriaList": [
      {
        "failureType": "string",
        "action": "string",
        "thresholdPercentage": "double",
        "minNumberOfExecutedThings": "integer"
      }
    ]
  },
  "awsJobTimeoutConfig": {
    "inProgressTimeoutInMinutes": "long"
  },
  "files": [
    {
      "fileName": "string",
      "fileType": "integer",
      "fileVersion": "string",
      "fileLocation": {
        "stream": {
          "streamId": "string",
          "fileId": "integer"
        },
        "s3Location": {
          "bucket": "string",
          "key": "string",
          "version": "string"
        }
      },
      "codeSigning": {
        "awsSignerJobId": "string",
        "startSigningJobParameter": {
          "signingProfileParameter": {
            "certificateArn": "string",
            "platform": "string",
            "certificatePathOnDevice": "string"
          },
          "signingProfileName": "string",
          "destination": {
            "s3Destination": {
              "bucket": "string",
              "prefix": "string"
            }
          }
        },
        "customCodeSigning": {
          "signature": {
            "inlineDocument": "blob"
          },
          "certificateChain": {
            "certificateName": "string",
            "inlineDocument": "string"
          },
          "hashAlgorithm": "string",
          "signatureAlgorithm": "string"
        }
      },
      "attributes": {
        "string": "string"
      }
    }
  ],
  "roleArn": "string",
  "additionalParameters": {
    "string": "string"
  },
  "tags": [
    {
      "Key": "string",
      "Value": "string"
    }
  ]
}
```


**`cli-input-json` fields**  

| Name | Type | Description | 
| --- | --- | --- | 
| `otaUpdateId` |  string  \(max:128 min:1\)  | The ID of the OTA update to be created\. | 
| `description` |  string  \(max:2028\)  | The description of the OTA update\. | 
| `targets` |  list  | The devices targeted to receive OTA updates\. | 
| `protocols` |  list  |  The protocol used to transfer the OTA update image\. Valid values are \[HTTP\], \[MQTT\], \[HTTP, MQTT\]\. When both HTTP and MQTT are specified, the target device can choose the protocol\.  | 
| `targetSelection` | string |  Specifies whether the update will continue to run \(CONTINUOUS\), or will be complete after all the things specified as targets have completed the update \(SNAPSHOT\)\. If continuous, the update may also be run on a thing when a change is detected in a target\. For example, an update will run on a thing when the thing is added to a target group, even after the update was completed by all things originally in the group\. Valid values: CONTINUOUS \| SNAPSHOT\. enum: CONTINUOUS \| SNAPSHOT  | 
| `awsJobExecutionsRolloutConfig` |  | Configuration for the rollout of OTA updates\. | 
| `maximumPerMinute` |  integer  \(max:1000 min:1\)  | The maximum number of OTA update job executions started per minute\. | 
| `exponentialRate` |  |  The rate of increase for a job rollout\. This parameter allows you to define an exponential rate increase for a job rollout\.  | 
| `baseRatePerMinute` |  integer  \(max:1000 min:1\)  |  The minimum number of things that will be notified of a pending job, per minute, at the start of the job rollout\. This is the initial rate of the rollout\.  | 
|   `rateIncreaseCriteria`  |   |  The criteria to initiate the increase in rate of rollout for a job\. AWS IoT supports up to one digit after the decimal \(for example, 1\.5, but not 1\.55\)\.  | 
|   `numberOfNotifiedThings`  |  integer  \(min:1\)  |  When this number of things have been notified, it will initiate an increase in the rollout rate\.  | 
|   `numberOfSucceededThings`  |  integer  \(min:1\)  |  When this number of things have succeeded in their job execution, it will initiate an increase in the rollout rate\.  | 
| `awsJobPresignedUrlConfig` |  |  Configuration information for pre\-signed URLs\. | 
|   `expiresInSec`  |  long |  How long \(in seconds\) pre\-signed URLs are valid\. Valid values are 60 \- 3600, the default value is 1800 seconds\. Pre\-signed URLs are generated when a request for the job document is received\.  | 
|   `awsJobAbortConfig`  |   |  The criteria that determine when and how a job abort takes place\. | 
|   `abortCriteriaList`  |  list  |  The list of criteria that determine when and how to abort the job\. | 
|   `failureType`  |  string |  The type of job execution failures that can initiate a job abort\.  enum: FAILED \| REJECTED \| TIMED\_OUT \| ALL  | 
|   `action`  |  string |  The type of job action to take to initiate the job abort\.  enum: CANCEL  | 
|   `minNumberOfExecutedThings`  |  integer  \(min:1\)  |  The minimum number of things which must receive job execution notifications before the job can be aborted\.  | 
|   `awsJobTimeoutConfig`  |   |  Specifies the amount of time each device has to finish its execution of the job\. A timer is started when the job execution status is set to `IN_PROGRESS`\. If the job execution status is not set to another terminal state before the timer expires, it will be automatically set to `TIMED_OUT`\.  | 
|   `inProgressTimeoutInMinutes`  |  long |  Specifies the amount of time, in minutes, this device has to finish execution of this job\. The timeout interval can be anywhere between 1 minute and 7 days \(1 to 10080 minutes\)\. The in progress timer can't be updated and will apply to all job executions for the job\. Whenever a job execution remains in the IN\_PROGRESS status for longer than this interval, the job execution will fail and switch to the terminal `TIMED_OUT` status\.  | 
|   `files`  |  list  |  The files to be streamed by the OTA update\. | 
|   `fileName`  |  string |  The name of the file\. | 
|   `fileType`  |  integer  range\- max:255 min:0  |  An integer value you can include in the job document to allow your devices to identify the type of file received from the cloud\.  | 
|   `fileVersion`  |  string |  The file version\. | 
|   `fileLocation`  |   |  The location of the updated firmware\. | 
|   `stream`  |   |  The stream that contains the OTA update\. | 
|   `streamId`  |  string  \(max:128 min:1\)  |  The stream ID\. | 
|   `fileId`  |  integer  \(max:255 min:0\)  |  The ID of a file associated with a stream\. | 
|   `s3Location`  |   |  The location of the updated firmware in S3\. | 
|   `bucket`  |  string  \(min:1\)  |  The S3 bucket\. | 
|   `key`  |  string  \(min:1\)  |  The S3 key\. | 
|   `version`  |  string |  The S3 bucket version\. | 
|   `codeSigning`  |   |  The code signing method of the file\. | 
|   `awsSignerJobId`  |  string |  The ID of the AWSSignerJob which was created to sign the file\. | 
|   `startSigningJobParameter`  |   |  Describes the code\-signing job\. | 
|   `signingProfileParameter`  |   |  Describes the code\-signing profile\. | 
|   `certificateArn`  |  string |  Certificate ARN\. | 
|   `platform`  |  string |  The hardware platform of your device\. | 
|   `certificatePathOnDevice`  |  string |  The location of the code\-signing certificate on your device\. | 
|   `signingProfileName`  |  string |  The code\-signing profile name\. | 
|   `destination`  |   |  The location to write the code\-signed file\. | 
|   `s3Destination`  |   |  Describes the location in S3 of the updated firmware\. | 
|   `bucket`  |  string  \(min:1\)  |  The S3 bucket that contains the updated firmware\. | 
|   `prefix`  |  string |  The S3 prefix\. | 
|   `customCodeSigning`  |   |  A custom method for code signing a file\. | 
|   `signature`  |   |  The signature for the file\. | 
|   `inlineDocument`  |  blob |  A base64 encoded binary representation of the code signing signature\. | 
|   `certificateChain`  |   |  The certificate chain\. | 
|   `certificateName`  |  string |  The name of the certificate\. | 
|   `inlineDocument`  |  string |  A base64 encoded binary representation of the code signing certificate chain\. | 
|   `hashAlgorithm`  |  string |  The hash algorithm used to code sign the file\. | 
|   `signatureAlgorithm`  |  string |  The signature algorithm used to code sign the file\. | 
|   `attributes`  |  map |  A list of name/attribute pairs\. | 
|   `roleArn`  |  string  \(max:2048 min:20\)  |  The IAM role that grants AWS IoT access to the Amazon S3, AWS IoT jobs and AWS Code Signing resources to create an OTA update job\. | 
|   `additionalParameters`  |  map |  A list of additional OTA update parameters which are name\-value pairs\. | 
|   `tags`  |  list  |  Metadata which can be used to manage updates\. | 
|   `Key`  |  string  \(max:128 min:1\)  |  The tag's key\. | 
|   `Value`  |  string  \(max:256 min:1\)  |  The tag's value\. | 

Output

```
{
  "otaUpdateId": "string",
  "awsIotJobId": "string",
  "otaUpdateArn": "string",
  "awsIotJobArn": "string",
  "otaUpdateStatus": "string"
}
```


**CLI output fields**  

|  Name |  Type |  Description | 
| --- | --- | --- | 
|   `otaUpdateId`  |  string  \(max:128 min:1\)  |  The OTA update ID\. | 
|   `awsIotJobId`  |  string |  The AWS IoT job ID associated with the OTA update\. | 
|   `otaUpdateArn`  |  string |  The OTA update ARN\. | 
|   `awsIotJobArn`  |  string |  The AWS IoT job ARN associated with the OTA update\. | 
|   `otaUpdateStatus`  |  string |  The OTA update status\.  enum: CREATE\_PENDING \| CREATE\_IN\_PROGRESS \| CREATE\_COMPLETE \| CREATE\_FAILED  | 

The following is an example of a JSON file passed into the create\-ota\-update command that uses Code Signing for AWS IoT\.

```
[
  {
    "fileName": "firmware.bin",                
    "fileType": 1,
    "fileLocation": {
      "stream": {
        "streamId": "004",                         
        "fileId":123
      }                        
    },
    "codeSigning": {
      "awsSignerJobId": "48c67f3c-63bb-4f92-a98a-4ee0fbc2bef6"     
    }
  }
]
```

The following is an example of a JSON file passed into the create\-ota\-update CLI command that uses an inline file to provide custom code\-signing material\.

```
[
  {
    "fileName": "firmware.bin",
    "fileType": 1,
    "fileLocation": {
      "stream": {
        "streamId": "004",
        "fileId": 123
      }
    },
    "codeSigning": {
      "customCodeSigning":{
        "signature":{
          "inlineDocument":"your_signature"
        },
        "certificateChain": {
          "certificateName": "your_certificate_name",
          "inlineDocument":"your_certificate_chain"
        },
        "hashAlgorithm":"your_hash_algorithm",
        "signatureAlgorithm":"your_signature_algorithm"
      }
    }
  }
]
```

The following is an example of a JSON file passed into the create\-ota\-update CLI command that allows FreeRTOS OTA to start a code\-signing job and create a code\-signing profile and stream\.

```
[
  {
    "fileName": "your_firmware_path_on_device",
    "fileType": 1,
    "fileVersion": "1",
    "fileLocation": {
      "s3Location": {
        "bucket": "your_bucket_name",
        "key": "your_object_key",
        "version": "your_S3_object_version"
      }
    },
    "codeSigning":{
      "startSigningJobParameter":{
        "signingProfileName": "myTestProfile",
        "signingProfileParameter": {
          "certificateArn": "your_certificate_arn",
          "platform": "your_platform_id",
          "certificatePathOnDevice": "certificate_path"
        },
        "destination": {
          "s3Destination": {
            "bucket": "your_destination_bucket"
          }
        }
      }
    }  
  }
]
```

The following is an example of a JSON file passed into the create\-ota\-update CLI command that creates an OTA update that starts a code\-signing job with an existing profile and uses the specified stream\.

```
[
  {
    "fileName": "your_firmware_path_on_device",
    "fileType": 1,
    "fileVersion": "1",
    "fileLocation": {
      "s3Location": {
        "bucket": "your_s3_bucket_name",
        "key": "your_object_key",
        "version": "your_S3_object_version"
      }
    },
    "codeSigning":{
      "startSigningJobParameter":{
        "signingProfileName": "your_unique_profile_name",
        "destination": {
          "s3Destination": {
            "bucket": "your_destination_bucket"
          }
        }
      }
    }  
  }
]
```

The following is an example of a JSON file passed into the create\-ota\-update CLI command that allows FreeRTOS OTA to create a stream with an existing code\-signing job ID\.

```
[
  {
    "fileName": "your_firmware_path_on_device",
    "fileType": 1,
    "fileVersion": "1",
    "codeSigning":{
      "awsSignerJobId": "your_signer_job_id"
    }  
  }
]
```

The following is an example of a JSON file passed into the create\-ota\-update CLI command that creates an OTA update\. The update creates a stream from the specified S3 object and uses custom code signing\.

```
[
  {
    "fileName": "your_firmware_path_on_device",
    "fileType": 1,
    "fileVersion": "1",
    "fileLocation": {
      "s3Location": {
        "bucket": "your_bucket_name",
        "key": "your_object_key",
        "version": "your_S3_object_version"
      }
    },
    "codeSigning":{
      "customCodeSigning": {
        "signature":{
          "inlineDocument":"your_signature"
        },
        "certificateChain": {
          "inlineDocument":"your_certificate_chain",
          "certificateName": "your_certificate_path_on_device"
        },
        "hashAlgorithm":"your_hash_algorithm",
        "signatureAlgorithm":"your_sig_algorithm"
      }
    }  
  }
]
```

**Note**  
If you see `"Error: You have exceeded the limit for the number of streams in your AWS account."`, then see [Stream limit exceeded for your AWS account](streaming-service.md#ota-troubleshooting-stream-limit)\.

## Listing OTA updates<a name="list-ota-updates"></a>

You can use the list\-ota\-updates CLI command to get a list of all OTA updates\.

```
aws iot list-ota-updates
```

The output from the list\-ota\-updates command looks like this\.

```
{
  "otaUpdates": [
     
    {
      "otaUpdateId": "my_ota_update2",
      "otaUpdateArn": "arn:aws:iot:us-west-2:123456789012:otaupdate/my_ota_update2",
      "creationDate": 1522778769.042
    },
    {
      "otaUpdateId": "my_ota_update1",
      "otaUpdateArn": "arn:aws:iot:us-west-2:123456789012:otaupdate/my_ota_update1",
      "creationDate": 1522775938.956
    },
    {
      "otaUpdateId": "my_ota_update",
      "otaUpdateArn": "arn:aws:iot:us-west-2:123456789012:otaupdate/my_ota_update",
      "creationDate": 1522775151.031
    }
  ]
}
```

## Getting information about an OTA update<a name="get-ota-updates"></a>

You can use the get\-ota\-update CLI command to get the creation or deletion status of an OTA update\.

```
aws iot get-ota-update --ota-update-id your-ota-update-id
```

The output from the get\-ota\-update command looks like the following\.

```
{ 
    "otaUpdateInfo": { 
        "otaUpdateId": "ota-update-001", 
        "otaUpdateArn": "arn:aws:iot:us-west-2:123456789012:otaupdate/ota-update-001", 
        "creationDate": 1575414146.286, 
        "lastModifiedDate": 1575414149.091, 
        "targets": [ 
            "arn:aws:iot:us-west-2:123456789012:thing/myDevice" 
        ], 
        "protocols": [ "HTTP" ], 
        "awsJobExecutionsRolloutConfig": { 
            "maximumPerMinute": 0 
        }, 
        "awsJobPresignedUrlConfig": { 
            "expiresInSec": 1800 
        }, 
        "targetSelection": "SNAPSHOT", 
        "otaUpdateFiles": [ 
            { 
                "fileName": "my_firmware.bin", 
                "fileType": 1,
                "fileLocation": { 
                    "s3Location": { 
                        "bucket": "my-bucket", 
                        "key": "my_firmware.bin", 
                        "version": "AvP3bfJC9gyqnwoxPHuTqM5GWENt4iii" 
                    } 
                }, 
                "codeSigning": { 
                    "awsSignerJobId": "b7a55a54-fae5-4d3a-b589-97ed103737c2", 
                    "startSigningJobParameter": { 
                        "signingProfileParameter": {}, 
                        "signingProfileName": "my-profile-name", 
                        "destination": { 
                            "s3Destination": { 
                                "bucket": "some-ota-bucket", 
                                "prefix": "SignedImages/" 
                            } 
                        } 
                    }, 
                    "customCodeSigning": {} 
                } 
            } 
        ], 
        "otaUpdateStatus": "CREATE_COMPLETE", 
        "awsIotJobId": "AFR_OTA-ota-update-001", 
        "awsIotJobArn": "arn:aws:iot:us-west-2:123456789012:job/AFR_OTA-ota-update-001" 
    } 
}
```

The values returned for `otaUpdateStatus` include the following:

`CREATE_PENDING`  
The creation of an OTA update is pending\.

`CREATE_IN_PROGRESS`  
An OTA update is being created\.

`CREATE_COMPLETE`  
An OTA update has been created\.

`CREATE_FAILED`  
The creation of an OTA update failed\.

`DELETE_IN_PROGRESS`  
An OTA update is being deleted\.

`DELETE_FAILED`  
The deletion of an OTA update failed\.

**Note**  
To get the execution status of an OTA update after it is created, you need to use the describe\-job\-execution command\. For more information, see [ Describe Job Execution](https://docs.aws.amazon.com/iot/latest/developerguide/manage-job-cli.html#describe-job-execution)\.

## Deleting OTA\-related data<a name="delete-ota-data"></a>

Currently, you cannot use the AWS IoT console to delete streams or OTA updates\. You can use the AWS CLI to delete streams, OTA updates, and the AWS IoT jobs created during an OTA update\.

### Deleting an OTA stream<a name="delete-ota-stream"></a>

When you create an OTA update that uses MQTT, either you can use the command\-line or the AWS IoT console to create a stream to break the firmware up into chunks so it can be sent over MQTT\. You can delete this stream with the delete\-stream CLI command, as shown in the following example\.

```
aws iot delete-stream --stream-id your_stream_id
```

### Deleting an OTA update<a name="delete-ota-update"></a>

When you create an OTA update, the following are created:
+ An entry in the OTA update job database\.
+ An AWS IoT job to perform the update\.
+ An AWS IoT job execution for each device being updated\.

The delete\-ota\-update command deletes the entry in the OTA update job database only\. You must use the delete\-job command to delete the AWS IoT job\.

Use the delete\-ota\-update command to delete an OTA update\.

```
aws iot delete-ota-update --ota-update-id your_ota_update_id
```

`ota-update-id`  
The ID of the OTA update to delete\.

`delete-stream`  
Deletes the stream associated with the OTA update\.

`force-delete-aws-job`  
Deletes the AWS IoT job associated with the OTA update\. If this flag is not set and the job is in the `In_Progress` state, the job is not deleted\.

### Deleting an IoT job created for an OTA update<a name="delete-ota-job"></a>

FreeRTOS creates an AWS IoT job when you create an OTA update\. A job execution is also created for each device that processes the job\. You can use the delete\-job CLI command to delete a job and its associated job executions\.

```
aws iot delete-job --job-id your-job-id --no-force
```

The `no-force` parameter specifies that only jobs that are in a terminal state \(COMPLETED or CANCELLED\) can be deleted\. You can delete a job that is in a non\-terminal state by passing the `force` parameter\. For more information, see [DeleteJob API](https://docs.aws.amazon.com/iot/latest/apireference/API_DeleteJob.html)\.

**Note**  
Deleting a job with a status of IN\_PROGRESS interrupts any job executions that are IN\_PROGRESS on your devices and can result in a device being left in a nondeterministic state\. Make sure that each device executing a job that has been deleted can recover to a known state\.

Depending on the number of job executions created for the job and other factors, it can take a few minutes to delete a job\. While the job is being deleted, its status is DELETION\_IN\_PROGRESS\. Attempting to delete or cancel a job whose status is already DELETION\_IN\_PROGRESS results in an error\.

You can use the delete\-job\-execution to delete a job execution\. You might want to delete a job execution when a small number of devices are unable to process a job\. This deletes the job execution for a single device, as shown in the following example\.

```
aws iot delete-job-execution --job-id your-job-id --thing-name
                    your-thing-name --execution-number your-job-execution-number --no-force
```

As with the delete\-job CLI command, you can pass the `--force` parameter to the delete\-job\-execution to force the deletion of a job execution\. For more information , see [DeleteJobExecution API](https://docs.aws.amazon.com/iot/latest/apireference/API_DeleteJobExecution.html)\.

**Note**  
Deleting a job execution with a status of IN\_PROGRESS interrupts any job executions that are IN\_PROGRESS on your devices and can result in a device being left in a nondeterministic state\. Make sure that each device executing a job that has been deleted can recover to a known state\.

For more information about using the OTA update demo application, see [Over\-the\-air updates demo application](ota-demo.md)\.
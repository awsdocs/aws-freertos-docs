# Creating an OTA update with the AWS CLI<a name="ota-cli-workflow"></a>

When you use the AWS CLI to create an OTA update, you:

1. Digitally sign your firmware image\.

1. Create a stream of your digitally signed firmware image\.

1. Start an OTA update job\.

## Digitally signing your firmware update<a name="ota-sign-cli"></a>

When you use the AWS CLI to perform OTA updates, you can use Code Signing for AWS IoT, or you can sign your firmware update yourself\. For a list of the cryptographic signing and hashing algorithms supported by Code Signing for AWS IoT, see [SigningConfigurationOverrides](https://docs.aws.amazon.com/signer/latest/api/API_SigningConfigurationOverrides.html)\. If you want to use a cryptographic algorithm that is not supported by Code Signing for AWS IoT, you must sign your firmware binary before you upload it to Amazon S3\.

### Signing your firmware image with Code Signing for AWS IoT<a name="ota-sign-csfa"></a>

To sign your firmware image using Code Signing for AWS IoT, you can use one of the [AWS SDKs or command line tools](https://aws.amazon.com/tools/)\. For more information about Code Signing for AWS IoT, see [Code Signing for AWS IoT](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.

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
The destination for the signed firmware in an S3 bucket\. The format of this parameter is the same as the `source` parameter\.

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
If you see `"Error: You have exceeded the limit for the number of streams in your AWS account."`, then see [Stream limit exceeded for your AWS account](ota-troubleshooting-stream-limit.md)\.

## Creating an OTA update<a name="create-ota-update"></a>

Use the create\-ota\-update CLI command to create an OTA update job\.

```
aws iot create-ota-update \
    --ota-update-id "my_ota_update" \
    --target-selection SNAPSHOT \
    --protocols "[MQTT, HTTP]"
    --description "a cli ota update" \
    --files file://ota.json \
    --targets arn:aws:iot:your-aws-region:your-aws-account:thing/your-thing-name \
    --role-arn arn:aws:iam::your-aws-account:role/your-ota-service-role
```

**Note**  
Do not use any personally identifiable information \(PII\) in your OTA update job ID\. Examples of personally identifiable information include:  
Names\.
IP addresses\.
Email addresses\.
Locations\.
Bank details\.
Medical information\.

`ota-update-id`  
An arbitrary OTA update ID\.

`target-selection`  
Valid values are:  
+ `SNAPSHOT`: The job terminates after deploying the update to the selected AWS IoT thing or groups\.
+ `CONTINUOUS`: The job continues to deploy updates to devices added to the selected groups\.

`protocols`  
A list of protocols that support transfer of the firmware image\. You can specify the following values:   
+ `MQTT`: The firmware image is transferred to the device using the MQTT protocol\.
+ `HTTP`: The firmware image is transferred to the device using the HTTP protocol\. The firmware image must be hosted on Amazon S3\.
+ `MQTT` and `HTTP`: The firmware image can be transferred to the device using the MQTT or the HTTP protocol\. The device can determine which protocol to use\.

`description`  
A text description of the OTA update\.

`files`  
One or more references to JSON files that contain data about the OTA update\. The JSON file can contain the following attributes:  
+ `fileName`: The fully qualified firmware image file name\. For Texas Instruments CC3220SF\-LAUNCHXL, this must be `/sys/mcuflashimg.bin`\. For Microchip, this must be `mplab.production.bin`\. 
+ `fileLocation`: Contains information about the firmware update\.
  + `stream`: The stream that contains the firmware update\.
    + `streamId`: The stream ID specified in the create\-stream CLI command\.
    + `fileId`: The file ID specified in the JSON file passed to create\-stream\.
  + `s3Location`: The location in Amazon S3 of the firmware update\.
    + `bucket`: The Amazon S3 bucket that contains the firmware update\.
    + `key`: The firmware update key\.
    + `version`: The firmware update version\.
+ `codeSigning`: Contains information about the code\-signing job\.
  + `awsSignerJobId`: The signer job ID generated by the start\-signing\-job command\.
  + `startSigningJobParamater`: The information required to start a code\-signing job\.
    + `signingProfileParameter`: The information required to create a signing job profile\.
      + `certificateArn`: The ACM ARN of the certificate used to create a code\-signing job\.
      + `platformId`: The ID of the hardware platform you are using\.
      + `certificatePathOnDevice`: The path to the certificate on your device\.
    + `signingProfileName`: The signing profile name\. If a profile with this name does not exist, you must provide values for `signingProfileParameter`\. If a profile with the specified name exists, and you provide values for `signingProfileParameter`, the values you provide must match exactly the values you used for the signing profile\.
    + `destination`: The location where the signed artifact is placed\.
      + `s3Destination`: The Amazon S3 bucket where the signed artifact is placed\.
        + `bucket`: The Amazon S3 bucket\.
        + `prefix`: The prefix of the code\-signing artifact\. By default, this is `signedImage/`\. This creates a folder called `signedImage` under your folder\.
  + `customCodeSigning`: Contains information about a custom signature\.
    + `signature`: Contains a custom signature\.
      + `inlineDocument`: The custom signature\.
    + `certificateChain`: Contains a certificate chain for a custom signature\.
      + `certificateName`: The path name of the code\-signing certificate on the device\.
      + `inlineDocument`: The certificate chain\.
    + `hashAlgorithm`: The hash algorithm used to create the signature\.
    + `signatureAlgorithm`: The signature algorithm used for code signing\.
  + `attributes`: Arbitrary key\-value pairs\.

`targets`  
One or more AWS IoT thing ARN that specifies the devices to be updated by the OTA update\.

`role-arn`  
The ARN of your service role\.

The following is an example of a JSON file passed into the create\-ota\-update command that uses Code Signing for AWS IoT\.

```
[
  {
    "fileName": "firmware.bin",                
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
If you see `"Error: You have exceeded the limit for the number of streams in your AWS account."`, then see [Stream limit exceeded for your AWS account](ota-troubleshooting-stream-limit.md)\.

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
To get the execution status of an OTA update after it is created, you need to use the describe\-job\-execution command\. For more information, see [Describe Job Execution](https://docs.aws.amazon.com/iot/latest/developerguide/manage-job-cli.html#describe-job-execution)\.

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
# Get OTA Failure Codes with the AWS CLI<a name="ota-failure-codes"></a>

1. Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)\. 

1. Run "`aws configure`" and enter following information: 

   ```
   $ aws configure
   AWS Access Key ID [None]: AccessID
   AWS Secret Access Key [None]: AccessKey
   Default region name [None]: Region
   Default output format [None]: json
   ```

1. Run: 

   ```
   aws iot describe-job-execution --job-id JobID --thing-name ThingName
   ```

   Where *JobID* is the complete job ID string for the job whose status we want to get and *ThingName* is the AWS IoT thing name that the device is registered as in AWS IoT

1. The output will look like this:

   ```
   {
       "execution": {
           "jobId": "AFR_OTA-****************",
           "status": "FAILED",
           "statusDetails": {
               "detailsMap": {
                   "reason": "0xEEEEEEEE: 0xffffffff"
               }
           },
           "thingArn": "arn:aws:iot:Region:AccountID:thing/ThingName",
           "queuedAt": 1569519049.9,
           "startedAt": 1569519052.226,
           "lastUpdatedAt": 1569519052.226,
           "executionNumber": 1,
           "versionNumber": 2
       }
   }
   ```

   In this example output, the "`reason`" in the "`detailsmap`" has two fields: the field shown as "0xEEEEEEEE" contains the generic error code from the OTA agent; the field shown as "0xffffffff" contains the sub\-code\. The generic error codes are listed in [ https://docs\.aws\.amazon\.com/freertos/latest/lib\-ref/html1/aws\_\_ota\_\_agent\_8h\.html](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__ota__agent_8h.html)\. See error codes with the prefix "`kOTA_Err_`"\. The sub\-code can be a platform specific code or provide more details about the generic error\. 
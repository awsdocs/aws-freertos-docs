# Get CreateOTAUpdate failure details using the AWS CLI<a name="ota-create-failure"></a>

If the process of creating an OTA update job fails, there may be actions you can take to remedy the problem\. When you create an OTA update job, the OTA manager service creates an IoT job and schedules it for the target devices, and this process also creates or uses other types of AWS resources in your account \(a code\-signing job, an AWS IoT stream, an Amazon S3 object\)\. Any error encountered may cause the process to fail without creating an AWS IoT job\. In this troubleshooting section we give instructions on how to retrieve the details of the failure\.

1. Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)\.

1. Run `aws configure` and enter the following information\.

   ```
   $ aws configure
   AWS Access Key ID [None]: AccessID
   AWS Secret Access Key [None]: AccessKey
   Default region name [None]: Region
   Default output format [None]: json
   ```

   For more information, see [ Quick configuration with aws configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config)\.

1. Run: 

   ```
   aws iot get-ota-update --ota-update-id ota_update_job_001
   ```

   Where *ota\_update\_job\_001* is the ID you gave the OTA update when you created it\.

1. The output will look like this:

   ```
   {
       "otaUpdateInfo": {
           "otaUpdateId": "ota_update_job_001",
           "otaUpdateArn": "arn:aws:iot:region:account_id:otaupdate/ota_update_job_001",
           "creationDate": 1584646864.534,
           "lastModifiedDate": 1584646865.913,
           "targets": [
               "arn:aws:iot:region:account_id:thing/thing_001"
           ],
           "protocols": [
               "MQTT"
           ],
           "awsJobExecutionsRolloutConfig": {},
           "awsJobPresignedUrlConfig": {},
           "targetSelection": "SNAPSHOT",
           "otaUpdateFiles": [
               {
                  "fileName": "/12ds",
                   "fileLocation": {
                       "s3Location": {
                           "bucket": "bucket_name",
                           "key": "demo.bin",
                           "version": "Z7X.TWSAS7JSi4rybc02nMdcE41W1tV3"
                       }
                   },
                   "codeSigning": {
                       "startSigningJobParameter": {
                           "signingProfileParameter": {},
                           "signingProfileName": "signing_profile_name",
                           "destination": {
                               "s3Destination": {
                                   "bucket": "bucket_name",
                                   "prefix": "SignedImages/"
                               }
                           }
                       },
                       "customCodeSigning": {}
                   }
               }
           ],
           "otaUpdateStatus": "CREATE_FAILED",
           "errorInfo": {
               "code": "AccessDeniedException",
               "message": "S3 object demo.bin not accessible. Please check your permissions (Service: AWSSigner; Status Code: 403; Error Code: AccessDeniedException; Request ID: 01d8e7a1-8c7c-4d85-9fd7-dcde975fdd2d)"
           }
       }
   }
   ```

   If the create failed, the `otaUpdateStatus` field in the command output will contain `CREATE_FAILED` and the `errorInfo` field will contain the details of the failure\.
# Create an Amazon S3 Bucket to Store Your Update<a name="dg-ota-bucket"></a>

OTA update files are stored in Amazon S3 buckets\. If you are using Code Signing for Amazon FreeRTOS, the command you use to create a code\-signing job takes a source bucket \(where the unsigned firmware image is located\) and a destination bucket \(where the signed firmware image is written\)\. You can specify the same bucket for both the source and destination\. The file names are changed to GUIDs so the original files are not overwritten\.<a name="create-bucket"></a>

**To create an Amazon S3 bucket**

1. Go to the [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. Type a bucket name, and then choose **Next**\.
**Note**  
Your bucket name must begin with `afr-ota`\.

1. On the **Create bucket** page, choose **Versioning**\.

1. Choose **Enable versioning**, choose **Save**, and then choose **Next**\.

1. Choose **Next** to accept the default permissions\.

1. Choose **Create bucket**\.

For more information about Amazon S3, see [Amazon Simple Storage Service Console User Guide](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/)\.
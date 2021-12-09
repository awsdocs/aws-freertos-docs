# coreHTTP basic Amazon S3 upload demo<a name="core-http-s3-upload-demo"></a>

## Introduction<a name="core-http-s3-upload-demo-intro"></a>

This example demonstrates how to send a PUT request to the Amazon Simple Storage Service \(Amazon S3\) HTTP server and upload a small file\. It also performs a GET request to verify the size of the file after the upload\. This example uses a [network transport interface](https://freertos.org/network-interface.html) that uses mbedTLS to establish a mutually authenticated connection between an IoT device client running coreHTTP and the Amazon S3 HTTP server\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

### Single threaded versus multi threaded<a name="core-http-s3-upload-demo-threads"></a>

There are two coreHTTP usage models, *single threaded* and *multithreaded* \(multitasking\)\. Although the demo in this section runs the HTTP library in a thread, it actually demonstrates how to use coreHTTP in a single threaded environment\. Only one task in this demo uses the HTTP API\. Although single threaded applications must repeatedly call the HTTP library, multithreaded applications can instead send HTTP requests in the background within an agent \(or daemon\) task\.

## Source code organization<a name="core-http-s3-upload-demo-source-code-organization"></a>

The demo source file is named `http_demo_s3_upload.c` and can be found in the `freertos/demos/coreHTTP/` directory and on the  [ GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreHTTP/http_demo_s3_upload.c) website\.

## Configuring the Amazon S3 HTTP server connection<a name="core-http-s3-upload-demo-configure-server"></a>

This demo uses a pre\-signed URL to connect to the Amazon S3 HTTP server and authorize access to the object to download\. The Amazon S3 HTTP server's TLS connection uses server authentication only\. At the application level, access to the object is authenticated with parameters in the pre\-signed URL query\. Follow the steps below to configure your connection to AWS\.

1. Set up an AWS account:

   1. If you haven't already, [ create an AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

   1. Accounts and permissions are set using AWS Identity and Access Management \(IAM\)\. You use IAM to manage permissions for each user in your account\. By default, a user doesn't have permissions until granted by the root owner\.

      1. To add an IAM user to your AWS account, see the [ IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

      1. Grant permission to your AWS account to access FreeRTOS and AWS IoT by adding this policy:
         + AmazonS3FullAccess

1. Create a bucket in Amazon S3 by following the steps in [How do I create an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\.

1. Upload a file to Amazon S3 by following the steps in [How do I upload files and folders to an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html)\.

1. Generate a pre\-signed URL using the script located at the `FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/Common/presigned_url_generator/presigned_urls_gen.py` file\.

   For usage instructions, see the `FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/Common/presigned_url_generator/README.md` file\. 

## Functionality<a name="core-http-s3-upload-demo-functionality"></a>

The demo first connects to the Amazon S3 HTTP server with TLS server authentication\. Then, it creates an HTTP request to upload the data specified in `democonfigDEMO_HTTP_UPLOAD_DATA`\. After uploading the file, it checks that file was successfully uploaded by requesting for the size of the file\. The source code for the demo can be found on the [GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreHTTP/http_demo_s3_upload.c) website\. 

### Connecting to the Amazon S3 HTTP server<a name="core-http-s3-upload-demo-connecting"></a>

The [ connectToServerWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/main/demos/common/http_demo_helpers/http_demo_utils.c#L131-L170) function attempts to make a TCP connection to the HTTP server\. If the connection fails, it retries after a timeout\. The timeout value will exponentially increase until the maximum number of attempts are reached or the maximum timeout value is reached\. The `connectToServerWithBackoffRetries` function returns a failure status if the TCP connection to the server can't be established after the configured number of attempts\.

The `prvConnectToServer` function demonstrates how to establish a connection to the Amazon S3 HTTP server by using server authentication only\. It uses the mbedTLS\-based transport interface that is implemented in the `FreeRTOS-Plus/Source/Application-Protocols/network_transport/freertos_plus_tcp/using_mbedtls/using_mbedtls.c` file\. The definition of `prvConnectToServer` can be found on the [GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreHTTP/http_demo_s3_upload.c#L306-L366) website\. 

### Upload data<a name="core-http-s3-upload-demo-upload-data"></a>

The `prvUploadS3ObjectFile` function demonstrates how to create a PUT request and specify the file to upload\. The Amazon S3 bucket where the file is uploaded and the name of file to upload are specified in the pre\-signed URL\. To save memory, the same buffer is used for both the request headers and to receive the response\. The response is received synchronously using the `HTTPClient_Send` API function\. A `200 OK` response status code is expected from the Amazon S3 HTTP server\. Any other status code is an error\.

The source code for `prvUploadS3ObjectFile()` can be found on the [ GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreHTTP/http_demo_s3_upload.c#L539-L632) website\.

### Verifying the upload<a name="core-http-s3-upload-demo-verifying-data"></a>

The `prvVerifyS3ObjectFileSize` function calls `prvGetS3ObjectFileSize` to retrieve the size of the object in the S3 bucket\. The Amazon S3 HTTP server doesn't currently support HEAD requests using a pre\-signed URL, so the 0th byte is requested\. The size of the file is contained in the response's `Content-Range` header field\. A `206 Partial Content` response is expected from the server\. Any other response status code is an error\. 

The source code for `prvGetS3ObjectFileSize()` can be found on the [ GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreHTTP/http_demo_s3_upload.c#L370-L535) website\. 
# coreHTTP basic S3 download demo<a name="core-http-s3-download-demo"></a>

## Introduction<a name="core-http-s3-download-demo-intro"></a>

This demo shows how to use [range requests](https://tools.ietf.org/html/rfc7233) to download files from the Amazon S3 HTTP server\. Range requests are natively supported in the coreHTTP API when you use `HTTPClient_AddRangeHeader` to create the HTTP request\. For a microcontroller environment, range requests are highly encouraged\. By downloading a large file in separate ranges, instead of in a single request, each section of the file can be processed without blocking the network socket\. Range requests lower the risk of having dropped packets, which require retransmissions on the TCP connection, and so they improve the power consumption of the device\.

This example uses a [network transport interface](https://freertos.org/network-interface.html) that uses mbedTLS to establish a mutually authenticated connection between an IoT device client running coreHTTP and the Amazon S3 HTTP server\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

### Single threaded versus multi threaded<a name="core-http-s3-download-demo-threads"></a>

There are two coreHTTP usage models, *single threaded* and *multithreaded* \(multitasking\)\. Although the demo in this section runs the HTTP library in a thread, it actually demonstrates how to use coreHTTP in a single threaded environment \(only one task uses the HTTP API in the demo\)\. Although single threaded applications must repeatedly call the HTTP library, multithreaded applications can instead send HTTP requests in the background within an agent \(or daemon\) task\.

## Source code organization<a name="core-http-s3-download-demo-source-code"></a>

The demo project is named `http_demo_s3_download.c` and can be found in the `freertos/demos/coreHTTP/` directory and on the [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_s3_download.c) website\. 

## Configuring the Amazon S3 HTTP server connection<a name="core-http-s3-download-demo-configure-server"></a>

This demo uses a pre\-signed URL to connect to the Amazon S3 HTTP server and authorize access to the object to download\. The Amazon S3 HTTP server's TLS connection uses server authentication only\. At the application level, access to the object is authenticated with parameters in the pre\-signed URL query\. Follow the steps below to configure your connection to AWS\.

1. Set up an Amazon Web Services \(AWS\) account:

   1. If you haven't already, [ create and activate an AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

   1. Accounts and permissions are set using AWS Identity and Access Management \(IAM\)\. IAM allows you to manage permissions for each user in your account\. By default, a user doesn't have permissions until granted by the root owner\.

      1. To add an IAM user to your AWS account, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

      1. Grant permission to your AWS account to access FreeRTOS and AWS IoT by adding these policies:
         + AmazonS3FullAccess

1. Create a bucket in S3 by following the steps in [How do I create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service Console User Guide*\.

1. Upload a file to S3 by following the steps in [How do I upload files and folders to an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html)\.

1. Generate a pre\-signed URL using the script located at `FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/Common/presigned_url_generator/presigned_urls_gen.py`\. For usage instructions, see `FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/Common/presigned_url_generato/README.md`\. 

## Functionality<a name="core-http-s3-download-demo-functionality"></a>

The demo retrieves the size of the file first\. Then it requests each byte range sequentially, in a loop, with range sizes of `democonfigRANGE_REQUEST_LENGTH`\.

Source code for the demo can be found on the [GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_s3_download.c) website\.

### Connecting to the Amazon S3 HTTP server<a name="core-http-s3-download-demo-connecting"></a>

The function [ connectToServerWithBackoffRetries\(\)](https://github.com/aws/amazon-freertos/blob/202012.00/demos/common/http_demo_helpers/http_demo_utils.c#L131-L170) attempts to make a TCP connection to the HTTP server\. If the connection fails, it retries after a timeout\. The timeout value will exponentially increase until the maximum number of attempts are reached or the maximum timeout value is reached\. `connectToServerWithBackoffRetries()` returns a failure status if the TCP connection to the server cannot be established after the configured number of attempts\. 

The function `prvConnectToServer()` demonstrates how to establish a connection to the Amazon S3 HTTP server using server authentication only\. It uses the mbedTLS\-based transport interface that is implemented in the file [ FreeRTOS\-Plus/Source/Application\-Protocols/network\_transport/freertos\_plus\_tcp/using\_mbedtls/using\_mbedtls\.c](https://github.com/FreeRTOS/FreeRTOS/blob/202012.00/FreeRTOS-Plus/Source/Application-Protocols/network_transport/freertos_plus_tcp/using_mbedtls/using_mbedtls.c)\. 

The source code for `prvConnectToServer()` can be found on [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_s3_download.c#L257-L318)\.

### Creating a range request<a name="core-http-s3-download-demo-creating-range-request"></a>

The API function `HTTPClient_AddRangeHeader()` supports serializing a byte range into the HTTP request headers to form a range request\. Range requests are used in this demo to retrieve the file size and to request each section of the file\.

The function `prvGetS3ObjectFileSize()` retrieves the size of the file in the S3 bucket\. The `Connection: keep-alive` header is added in this first request to Amazon S3 to keep the connection open after the response is sent\. The S3 HTTP server does not currently support HEAD requests using a pre\-signed URL, so the 0th byte is requested\. The size of the file is contained in the response's `Content-Range` header field\. A `206 Partial Content` response is expected from the server; any other response status\-code received is an error\.

The source code for `prvGetS3ObjectFileSize()` can be found on [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_s3_download.c#L321-L486)\.

After it retrieves the file size, this demo creates a new range request for each byte range of the file to download\. It uses `HTTPClient_AddRangeHeader()` for each section of the file\. 

### Sending range requests and receiving responses<a name="core-http-s3-download-demo-send-request"></a>

The function `prvDownloadS3ObjectFile()` sends the range requests in a loop until the entire file is downloaded\. The API function `HTTPClient_Send()` sends a request and receives the response synchronously\. When the function returns, the response is received in an `xResponse`\. The status\-code is then verified to be `206 Partial Content` and the number of bytes downloaded so far is incremented by the `Content-Length` header value\. 

The source code for `prvDownloadS3ObjectFile()` can be found on [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_s3_download.c#L490-L635)\.
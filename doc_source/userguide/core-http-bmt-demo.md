# coreHTTP basic multithreaded demo<a name="core-http-bmt-demo"></a>

## Introduction<a name="core-http-bmt-demo-intro"></a>

This demo uses [FreeRTOS's thread\-safe queues](https://freertos.org/a00018.html) to hold requests and responses waiting to be processed\. In this demo, there are three tasks to take note of\.
+ The main task waits for requests to appear in the request queue\. It will send those requests over the network, then place the response into the response queue\.
+ A request task creates HTTP library request objects to send to the server and places them into the request queue\. Each request object specifies a byte range of the S3 file that the application has configured for download\.
+ A response task waits for responses to appear in the response queue\. It logs every response it receives\.

This basic multithreaded demo is configured to use a TLS connection with server authentication only, this is required by the Amazon S3 HTTP server\. Application layer authentication is done using the [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) parameters in the [presigned URL query](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html)\.

## Source code organization<a name="core-http-bmt-demo-source"></a>

The demo project is named `http_demo_s3_download_multithreaded.c` and can be found in the `freertos/demos/coreHTTP/` directory and the [ GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreHTTP/http_demo_s3_download_multithreaded.c) website\.

## Building the demo project<a name="core-http-bmt-demo-building"></a>

The demo project uses the [free community edition of Visual Studio](https://visualstudio.microsoft.com/vs/community/)\. To build the demo:

1. Open the `mqtt_multitask_demo.sln` Visual Studio solution file from within the Visual Studio IDE\.

1. Select **Build Solution** from the IDE's **Build** menu\.

**Note**  
If you are using Microsoft Visual Studio 2017 or earlier, then you must select a **Platform Toolset** compatible with your version: **Project \-> RTOSDemos Properties \-> Platform Toolset**\.

## Configuring the demo project<a name="core-http-bmt-demo-configuring"></a>

The demo uses the [FreeRTOS\+TCP TCP/IP stack](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html), so follow the instructions provided for the [TCP/IP starter project](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/examples_FreeRTOS_simulator.html) to:

1. Install the [ pre\-requisite components](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/examples_FreeRTOS_simulator.html#prerequisites) \(such as WinPCap\)\.

1. Optionally [ set a static or dynamic IP address, gateway address and netmask](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/examples_FreeRTOS_simulator.html#static-dynamic)\.

1. Optionally [ set a MAC address](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/examples_FreeRTOS_simulator.html#mac-addr)\.

1. [ Select an Ethernet network interface](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/examples_FreeRTOS_simulator.html#network-interface) on your host machine\.

1. **Importantly** [ test your network connection](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/examples_FreeRTOS_simulator.html#connectivity-test) before attempting to run the HTTP demo\.

## Configuring the Amazon S3 HTTP server connection<a name="core-http-bmt-demo-configuring-connection"></a>

Follow the instructions for [Configuring the Amazon S3 HTTP server connection](core-http-s3-download-demo.md#core-http-s3-download-demo-configure-server) in the *coreHTTP basic download demo*\.

## Functionality<a name="core-http-bmt-demo-functionality"></a>

The demo creates three tasks in total: 
+ One that sends requests and receives responses over the network\.
+ One that creates requests to send\.
+ One that processes the received responses\.

In this demo, the primary task: 

1. Creates the request and response queues\. 

1. Creates the connection to the server\.

1. Creates the request and response tasks\.

1. Waits for the request queue to send requests over the network\.

1. Places responses received over the network into the response queue\.

The request task:

1. Creates each of the range requests\.

The response task:

1. Processes each of the responses received\.

## Typedefs<a name="core-http-bmt-demo-typedefs"></a>

The demo defines the following structures to support multithreading\.

**Request items**

The following structures define a request item to place into the request queue\. The request item is copied into the queue after the request task creates an HTTP request\.

```
/**
 * @brief Data type for the request queue.
 *
 * Contains the request header struct and its corresponding buffer, to be
 * populated and enqueued by the request task, and read by the main task. The
 * buffer is included to avoid pointer inaccuracy during queue copy operations.
 */
typedef struct RequestItem
{
    HTTPRequestHeaders_t xRequestHeaders;
    uint8_t ucHeaderBuffer[ democonfigUSER_BUFFER_LENGTH ];
} RequestItem_t;
```

**Response Item**

The following structures define a response item to place into the response queue\. The response item is copied into the queue after the main HTTP task receives a response over the network\.

```
/**
 * @brief Data type for the response queue.
 *
 * Contains the response data type and its corresponding buffer, to be enqueued
 * by the main task, and interpreted by the response task. The buffer is
 * included to avoid pointer inaccuracy during queue copy operations.
 */
typedef struct ResponseItem
{
    HTTPResponse_t xResponse;
    uint8_t ucResponseBuffer[ democonfigUSER_BUFFER_LENGTH ];
} ResponseItem_t;
```

## Main HTTP send task<a name="core-http-bmt-demo-main-task"></a>

The main application task:

1. Parses the presigned URL for the host address to establish a connection with the Amazon S3 HTTP server\.

1. Parses the presigned URL for the path to the objects in the S3 bucket\.

1. Connects to the Amazon S3 HTTP server using TLS with server authentication\.

1. Creates the request and response queues\.

1. Creates the request and response tasks\.

The function `prvHTTPDemoTask()` does this set up, and gives the demo status\. The source code for this function can be found on [ Github](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/HTTP_S3_Download_Multithreaded/DemoTasks/S3DownloadMultithreadedHTTPExample.c#L451-L650)\.

In the function `prvDownloadLoop()`, the main task blocks and waits on requests from the request queue\. When it receives a request it sends it using API function `HTTPClient_Send()`\. If the API function was successful, then it places the response into the response queue\. 

The source code for `prvDownloadLoop()` can be found on [ Github\.](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/HTTP_S3_Download_Multithreaded/DemoTasks/S3DownloadMultithreadedHTTPExample.c#L1071-L1174)

## HTTP request task<a name="core-http-bmt-demo-request-task"></a>

The request task is specified in the function `prvRequestTask`\. The source code for this function can be found on [ Github](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/HTTP_S3_Download_Multithreaded/DemoTasks/S3DownloadMultithreadedHTTPExample.c#L778-L876)\.

The request task retrieves the size of the file in the Amazon S3 bucket\. This is done in the function `prvGetS3ObjectFileSize`\. The "Connection: keep\-alive" header is added to this request to Amazon S3 to keep the connection open after the response is sent\. The Amazon S3 HTTP server does not currently support HEAD requests using a presigned URL, so the 0th byte is requested\. The size of the file is contained in the response's `Content-Range` header field\. A `206 Partial Content` response is expected from the server; any other response status\-code received is an error\. 

The source code for `prvGetS3ObjectFileSize` can be found on [ Github](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/HTTP_S3_Download_Multithreaded/DemoTasks/S3DownloadMultithreadedHTTPExample.c#L757-L774)\.

After retrieving the file size, the request task continues to request each range of the file\. Each range request is placed into the request queue for the main task to send\. The file ranges are configured by the demo user in the macro `democonfigRANGE_REQUEST_LENGTH`\. Range requests are natively supported in the HTTP client library API using the function `HTTPClient_AddRangeHeader`\. The function `prvRequestS3ObjectRange` demonstrates how to use `HTTPClient_AddRangeHeader()`\.

The source code for the function `prvRequestS3ObjectRange` can be found on [ Github](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/HTTP_S3_Download_Multithreaded/DemoTasks/S3DownloadMultithreadedHTTPExample.c#L694-L753)\.

## HTTP response task<a name="core-http-bmt-demo-response-task"></a>

The response tasks waits on the response queue for responses received over the network\. The main task populates the response queue when it successfully receives an HTTP response\. This task processes the responses by logging the status\-code, headers, and body\. A real\-world application may process the response by writing the response body to flash memory, for example\. If the response status\-code is not `206 partial content`, then the task notifies the main task that the demo should fail\. The response task is specified in function `prvResponseTask`\. The source code for this function can be found on [ Github](https://github.com/FreeRTOS/FreeRTOS/blob/main/FreeRTOS-Plus/Demo/coreHTTP_Windows_Simulator/HTTP_S3_Download_Multithreaded/DemoTasks/S3DownloadMultithreadedHTTPExample.c#L961-L1047)\.
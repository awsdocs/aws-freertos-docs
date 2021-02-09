# coreHTTP mutual authentication demo<a name="core-http-ma-demo"></a>

## Introduction<a name="core-http-ma-demo-intro"></a>

The coreHTTP \(Mutual Authentication\) demo project shows you how to establish a connection to an HTTP server using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server\- and client\-authenticated TLS connection, and demonstrates a request response workflow in HTTP\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Functionality<a name="core-http-ma-demo-functionality"></a>

This demo creates a single application task with examples that show how to complete the following:
+ Connect to the HTTP server on the AWS IoT endpoint\.
+ Send a POST request\.
+ Receive the response\.
+ Disconnect from the server\.

After you complete these steps, the demo generates output similar to the following screenshot\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coreHTTP.output.png)

The AWS IoT console generates output similar to the following screenshot\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coreHTTP.console.png)

## Source code organization<a name="core-http-s3-ma-demo-source-code-organization"></a>

The demo source file is named `http_demo_mutual_auth.c` and can be found in the `freertos/demos/coreHTTP/` directory and on the [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_mutual_auth.c) website\.

## Connecting to the AWS IoT HTTP server<a name="core-http-ma-demo-connecting"></a>

The [ connectToServerWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/202012.00/demos/common/http_demo_helpers/http_demo_utils.c#L131-L170) function attempts to make a mutually authenticated TLS connection to the AWS IoT HTTP server\. If the connection fails, it retries after a timeout\. The timeout value exponentially increases until the maximum number of attempts is reached or the maximum timeout value is reached\. The `RetryUtils_BackoffAndSleep` function provides exponentially increasing timeout values and returns `RetryUtilsRetriesExhausted` when the maximum number of attempts have been reached\. The `connectToServerWithBackoffRetries` function returns a failure status if the TLS connection to the broker can't be established after the configured number of attempts\.

## Sending an HTTP request and receiving the response<a name="core-http-ma-demo-send-receive"></a>

The [ prvSendHttpRequest](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreHTTP/http_demo_mutual_auth.c#L382-L487) function demonstrates how to send a POST request to the AWS IoT HTTP server\. For more information on making a request to the REST API in AWS IoT, see [Device communication protocols \- HTTPS](https://docs.aws.amazon.com/iot/latest/developerguide/http.html)\. The response is received with the same coreHTTP API call, `HTTPClient_Send`\. 
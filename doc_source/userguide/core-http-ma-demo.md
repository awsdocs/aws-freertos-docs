# coreHTTP mutual authentication demo<a name="core-http-ma-demo"></a>

## Introduction<a name="core-http-ma-demo-intro"></a>

The coreHTTP \(Mutual Authentication\) demo project shows you how to establish a connection to an HTTP server using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server\- and client\-authenticated TLS connection, and demonstrates a request response workflow in HTTP\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Functionality<a name="core-http-ma-demo-functionality"></a>

This demo creates a single application task with examples that show how to complete the following:
+ Connect to the HTTP server on the AWS IoT endpoint
+ Send a POST request
+ Receive the response
+ Disconnect from the server

After you complete these steps, the demo generates output similar to the following screenshot\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coreHTTP.output.png)

The AWS IoT console generates output similar to the following screenshot\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coreHTTP.console.png)

The following example is the structure of the demo\.

```
int RunCoreHttpMutualAuthDemo( bool awsIotMqttMode,
                               const char * pIdentifier,
                               void * pNetworkServerInfo,
                               void * pNetworkCredentialInfo,
                               const IotNetworkInterface_t * pNetworkInterface )
{
    /* The transport layer interface used by the HTTP Client library. */
    TransportInterface_t xTransportInterface;
    /* The network context for the transport layer interface. */
    NetworkContext_t xNetworkContext = { 0 };
    TransportSocketStatus_t xNetworkStatus;
    BaseType_t xIsConnectionEstablished = pdFALSE;

    /* Upon return, pdPASS will indicate a successful demo execution.
     * pdFAIL will indicate some failures occurred during execution. The
     * user of this demo must check the logs for any failure codes. */
    BaseType_t xDemoStatus = pdPASS;

    /* Remove compiler warnings about unused parameters. */
    ( void ) awsIotMqttMode;
    ( void ) pIdentifier;
    ( void ) pNetworkServerInfo;
    ( void ) pNetworkCredentialInfo;
    ( void ) pNetworkInterface;

    /**************************** Connect. ******************************/

    /* Attempt to connect to the HTTP server. If connection fails, retry after a
     * timeout. The timeout value will be exponentially increased until either 
     * the maximum number of attempts or the maximum timeout value is reached. 
     * The function returns pdFAIL if the TCP connection cannot be established 
     * with the broker after configured number of attempts. */
    xDemoStatus = connectToServerWithBackoffRetries( prvConnectToServer,
                                                     &xNetworkContext );

    if( xDemoStatus == pdPASS )
    {
        /* Set a flag indicating that a TLS connection exists. */
        xIsConnectionEstablished = pdTRUE;

        /* Define the transport interface. */
        xTransportInterface.pNetworkContext = &xNetworkContext;
        xTransportInterface.send = SecureSocketsTransport_Send;
        xTransportInterface.recv = SecureSocketsTransport_Recv;
    }
    else
    {
        /* Log error to indicate connection failure after all
         * reconnect attempts are over. */
        LogError( ( "Failed to connect to HTTP server %.*s.",
                    ( int32_t ) httpexampleAWS_IOT_ENDPOINT_LENGTH,
                    democonfigAWS_IOT_ENDPOINT ) );
    }

    /*********************** Send HTTP request.************************/

    if( xDemoStatus == pdPASS )
    {
        xDemoStatus = prvSendHttpRequest( &xTransportInterface,
                                          HTTP_METHOD_POST,
                                          httpexampleHTTP_METHOD_POST_LENGTH,
                                          democonfigPOST_PATH,
                                          httpexamplePOST_PATH_LENGTH );
    }

    /**************************** Disconnect. ******************************/

    /* Close the network connection to clean up any system resources that the
     * demo may have consumed. */
    if( xIsConnectionEstablished == pdTRUE )
    {
        /* Close the network connection.  */
        xNetworkStatus = SecureSocketsTransport_Disconnect( &xNetworkContext );

        if( xNetworkStatus != TRANSPORT_SOCKET_STATUS_SUCCESS )
        {
            xDemoStatus = pdFAIL;
            LogError( ( "SecureSocketsTransport_Disconnect() failed to close the network connection. "
                        "StatusCode=%d.", ( int ) xNetworkStatus ) );
        }
    }

    if( xDemoStatus == pdPASS )
    {
        LogInfo( ( "Demo completed successfully." ) );
    }

    return ( xDemoStatus == pdPASS ) ? EXIT_SUCCESS : EXIT_FAILURE;
}
```

## Connecting to the AWS IoT HTTP server<a name="core-http-ma-demo-connecting"></a>

The [ connectToServerWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/master/demos/common/http_demo_helpers/http_demo_utils.c#L131-L170) function attempts to make a mutually authenticated TLS connection to the AWS IoT HTTP server\. If the connection fails, it retries after a timeout\. The timeout value exponentially increases until the maximum number of attempts is reached or the maximum timeout value is reached\. The `RetryUtils_BackoffAndSleep` function provides exponentially increasing timeout values and returns `RetryUtilsRetriesExhausted` when the maximum number of attempts have been reached\. The `connectToServerWithBackoffRetries` function returns a failure status if the TLS connection to the broker can't be established after the configured number of attempts\.

## Sending an HTTP request and receiving the response<a name="core-http-ma-demo-send-receive"></a>

The `prvSendHttpRequest` function demonstrates how to send a POST request to the AWS IoT HTTP server\. For more information on making a request to the REST API in AWS IoT, see [Device communication protocols \- HTTPS](https://docs.aws.amazon.com/iot/latest/developerguide/http.html)\. The response is received with the same coreHTTP API call, `HTTPClient_Send`\. 

```
static BaseType_t prvSendHttpRequest( const TransportInterface_t * pxTransportInterface,
                                      const char * pcMethod,
                                      size_t xMethodLen,
                                      const char * pcPath,
                                      size_t xPathLen )
{
    /* Return value of this method. */
    BaseType_t xStatus = pdPASS;

    /* Configurations of the initial request headers that are passed to
     * #HTTPClient_InitializeRequestHeaders. */
    HTTPRequestInfo_t xRequestInfo;
    /* Represents a response returned from an HTTP server. */
    HTTPResponse_t xResponse;
    /* Represents header data that will be sent in an HTTP request. */
    HTTPRequestHeaders_t xRequestHeaders;

    /* Return value of all methods from the HTTP Client library API. */
    HTTPStatus_t xHTTPStatus = HTTPSuccess;

    configASSERT( pcMethod != NULL );
    configASSERT( pcPath != NULL );

    /* Initialize all HTTP Client library API structs to 0. */
    ( void ) memset( &xRequestInfo, 0, sizeof( xRequestInfo ) );
    ( void ) memset( &xResponse, 0, sizeof( xResponse ) );
    ( void ) memset( &xRequestHeaders, 0, sizeof( xRequestHeaders ) );

    /* Initialize the request object. */
    xRequestInfo.pHost = democonfigAWS_IOT_ENDPOINT;
    xRequestInfo.hostLen = httpexampleAWS_IOT_ENDPOINT_LENGTH;
    xRequestInfo.pMethod = pcMethod;
    xRequestInfo.methodLen = xMethodLen;
    xRequestInfo.pPath = pcPath;
    xRequestInfo.pathLen = xPathLen;

    /* Set "Connection" HTTP header to "keep-alive" so that multiple requests
     * can be sent over the same established TCP connection. */
    xRequestInfo.reqFlags = HTTP_REQUEST_KEEP_ALIVE_FLAG;

    /* Set the buffer used for storing request headers. */
    xRequestHeaders.pBuffer = ucUserBuffer;
    xRequestHeaders.bufferLen = democonfigUSER_BUFFER_LENGTH;

    xHTTPStatus = HTTPClient_InitializeRequestHeaders( &xRequestHeaders,
                                                       &xRequestInfo );

    if( xHTTPStatus == HTTPSuccess )
    {
        /* Initialize the response object. The same buffer used for storing
         * request headers is reused here. */
        xResponse.pBuffer = ucUserBuffer;
        xResponse.bufferLen = democonfigUSER_BUFFER_LENGTH;

        LogInfo( ( "Sending HTTP %.*s request to %.*s%.*s...",
                   ( int32_t ) xRequestInfo.methodLen, xRequestInfo.pMethod,
                   ( int32_t ) httpexampleAWS_IOT_ENDPOINT_LENGTH, democonfigAWS_IOT_ENDPOINT,
                   ( int32_t ) xRequestInfo.pathLen, xRequestInfo.pPath ) );
        LogDebug( ( "Request Headers:\n%.*s\n"
                    "Request Body:\n%.*s\n",
                    ( int32_t ) xRequestHeaders.headersLen,
                    ( char * ) xRequestHeaders.pBuffer,
                    ( int32_t ) httpexampleREQUEST_BODY_LENGTH, democonfigREQUEST_BODY ) );

        /* Send the request and receive the response. */
        xHTTPStatus = HTTPClient_Send( pxTransportInterface,
                                       &xRequestHeaders,
                                       ( uint8_t * ) democonfigREQUEST_BODY,
                                       httpexampleREQUEST_BODY_LENGTH,
                                       &xResponse,
                                       0 );
    }
    else
    {
        LogError( ( "Failed to initialize HTTP request headers: Error=%s.",
                    HTTPClient_strerror( xHTTPStatus ) ) );
                                                                                                                                                                                                                                                               }

    if( xHTTPStatus == HTTPSuccess )
    {
        LogInfo( ( "Received HTTP response from %.*s%.*s...\n",
                   ( int32_t ) httpexampleAWS_IOT_ENDPOINT_LENGTH, democonfigAWS_IOT_ENDPOINT,
                   ( int32_t ) xRequestInfo.pathLen, xRequestInfo.pPath ) );
                   LogDebug( ( "Response Headers:\n%.*s\n",
                   ( int32_t ) xResponse.headersLen, xResponse.pHeaders ) );
        LogDebug( ( "Status Code:\n%u\n",
                    xResponse.statusCode ) );
        LogDebug( ( "Response Body:\n%.*s\n",
                    ( int32_t ) xResponse.bodyLen, xResponse.pBody ) );
    }
    else
    {
        LogError( ( "Failed to send HTTP %.*s request to %.*s%.*s: Error=%s.",
                    ( int32_t ) xRequestInfo.methodLen, xRequestInfo.pMethod,
                    ( int32_t ) httpexampleAWS_IOT_ENDPOINT_LENGTH, democonfigAWS_IOT_ENDPOINT,
                    ( int32_t ) xRequestInfo.pathLen, xRequestInfo.pPath,
                    HTTPClient_strerror( xHTTPStatus ) ) );
    }

    if( xHTTPStatus != HTTPSuccess )
    {
        xStatus = pdFAIL;
    }

    return xStatus;
}
```
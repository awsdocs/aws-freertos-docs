# Secure Sockets<a name="secure-sockets"></a>

The Secure Sockets interface is based on the Berkeley socket interface\. It is provided for the easy onboarding of software developers from various network programming backgrounds\. The reference implementation for Secure Sockets supports TLS and TCP/IP over Ethernet and Wi\-Fi\. See `aws_secure_sockets.h` in the Amazon FreeRTOS source code repository\. 

## Secure Sockets Ports<a name="secure-socket-ports"></a>

This section contains information about Secure Socket ports for Amazon FreeRTOS\-qualified boards\. For information about creating your own port for Amazon FreeRTOS, see the [Amazon FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/userguide/porting-guide.html)\.

### STM32 IoT Discovery Kit Secure Sockets Port<a name="stm-sockets-port"></a>
+ This port supports up to four sockets\.
+ `SOCKETS_PERIPHERAL_RESET` means that the Wi\-Fi module has been reset\. This occurs when the Wi\-Fi module stops responding or gets out of sync with the SPI driver\. Call `WiFi_ConnectAP` to reconnect to your Wi\-Fi network\.

#### `Sockets_Connect`<a name="stm-socket-connect"></a>
+ `SocketsSockaddr_t` uses the `usPort` and `ulAddress` fields only\. `ucLength` and `ucSocketDomain` are not used\.
+ Supports IPv4 only\.
+ Sends connection information to the Wi\-Fi module only\. A successful return does not guarantee that the socket was able to reach the provided IP address\.

#### `Sockets_SetSockOpt`<a name="stm-sockets-opt"></a>

For `SOCKETS_SO_SNDTIMEO` and `SOCKETS_SO_RCVTIMEO`, valid values are 0 \(block forever\) and 30,000 milliseconds\.

#### SOCKETS\_Shutdown<a name="stm-sockets-shutdown"></a>

`SOCKETS_Shutdown` does not send a FIN packet, but does prevent the socket from being used for send and receive\.

### TI CC3220SF\-LAUNCHXL Secure Sockets Port<a name="ti-socket-port"></a>

This port supports up to 16 sockets\. The sockets can be secured with TLS\.

#### `Sockets_Connect`<a name="ti-socket-port-connect"></a>
+ `SocketsSockaddr_t` uses the `usPort` and `ulAddress` fields only\. `ucLength` and `ucSocketDomain` are not used\.
+ Supports IPv4 only\.
+ Receiving a negative error code from `SOCKETS_Connect` does not mean that the socket was closed\. Applications must close sockets after they receive a negative error code\.
+ When using a TLS\-enabled socket, sometimes a connection is made even though `SOCKETS_Connect` returned an error\. This might indicate that the connection cannot be authenticated using the provided root of trust\. We strongly recommend that you explicitly close the socket if a handshake\-related error is returned, even if the connection is made\.
+ In the event of handshake error, you can get information by enabling printing or by investigating the asynchronous event handler structure set in `SimpleLinkSockEventHandler`\.

#### `Sockets_SetSockOpt`<a name="ti-socket-port-opt"></a>

`SOCKETS_SO_RCVTIMEO` can be specified in 10\-millisecond increments\.

`SOCKETS_SO_SNDTIMEO` is not used\. It might be used in future versions\.

#### SOCKETS\_Send<a name="ti-socket-port-shutdown"></a>

In the event of a TX error, you can get information by investigating the TX Failed event handler structure in `SimpleLinkSockEventHandler`\.

#### SOCKETS\_Shutdown<a name="ti-socket-port-shutdown2"></a>

`SOCKETS_Shutdown` does not send a FIN packet, but does prevent the socket from being used for send and receive\.
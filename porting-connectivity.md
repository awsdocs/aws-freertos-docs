# Connectivity<a name="porting-connectivity"></a>

You must first configure your connectivity peripheral\. You can use Wi\-Fi, Bluetooth, Ethernet, or other connectivity mediums\. At this time, only a Wi\-Fi management API is defined for board ports, but if you are using Ethernet, the [FreeRTOS TCP/IP software ](http://www.freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html) can provide a good place to start\. 

## Wi\-Fi Management<a name="porting-connectivity-wifi"></a>

The Wi\-Fi management library supports network connectivity following the 802\.11 \(a/b/n\) protocol\. If your hardware does not support Wi\-Fi, you do not need to port this library\.

The functions that must be ported are listed in the `lib/wifi/portable/<vendor>/<platform>/aws_wifi.c` file\. You can find a detailed explanation for each public interface in `lib/include/aws_wifi.h`\.

The following functions must be ported:

```
WiFiReturnCode_t WIFI_On( void );
WIFIReturnCode_t WIFI_Off( void );
WiFiReturnCode_t WIFI_ConnectAP( const WiFiNetworkParams_t * const pxNetworkParams );
WiFiReturnCode_t WIFI_Disconnect( void );
WiFiReturnCode_t WIFI_Reset( void );
WiFiReturnCode_t WIFI_Scan( WiFiScanResult_t * pxBuffer, uint8_t uxNumNetworks );
```

## Sockets<a name="porting-sockets"></a>

The sockets library supports TCP/IP network communication between your board and another node in the network\. The sockets APIs are based on the Berkeley sockets interface, but also include a secure communication option\. At this time, only client APIs are supported\. We recommend that you port the TCP/IP functionality first, before you add the TLS functionality\.

Libraries for MQTT, Shadow, and Greengrass all make calls into the sockets layer\. A successful port of the sockets layer allows the protocols built on sockets to just work\.

### Major Differences from Berkeley Sockets Implementation<a name="diff-from-berkeley"></a>

#### Security<a name="berkeley-security"></a>

The sockets interface must be configured to use TLS for secure communication\. The `SetSockOpt` command has a couple of nonstandard options that must be implemented to work with AmazonFreeRTOS examples\.

```
SOCKETS_SO_REQUIRE_TLS  
SOCKETS_SO_SERVER_NAME_INDICATION 
SOCKETS_SO_TRUSTED_SERVER_CERTIFICATE
```

For information about these nonstandard options, see the [secure sockets documentation](secure-sockets.md)\. For information about porting TLS and cryptographic operations, see the [TLS](security-tls.md) and [Public Key Cryptography Standard \#11](security-pkcs.md) sections\.

#### Error Codes<a name="berkeley-error-codes"></a>

The SOCKETS library returns error codes from the API \(rather than setting a global errno\)\. All error codes returned must be negative values\.

The public interfaces that must be ported are listed in `lib/secure_sockets/portable/<vendor> /<platform>/aws_secure_sockets.c`\.

A detailed explanation for each public interface can be found in `lib/include/aws_secure_sockets.h`\.

If you are using TLS based on mbed TLS, you can save refactoring effort by implementing network send and network receive functions that can be registered with the TLS layer for sending and receiving plaintext or encrypted buffers\.
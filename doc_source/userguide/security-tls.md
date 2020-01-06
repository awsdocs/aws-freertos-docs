# Amazon FreeRTOS Transport Layer Security<a name="security-tls"></a>

The Amazon FreeRTOS Transport Layer Security \(TLS\) interface is a thin, optional wrapper used to abstract cryptographic implementation details away from the Secure Sockets Layer \(SSL\)  interface above it in the protocol stack\. The purpose of the TLS interface is to make the current software crypto library, mbed TLS, easy to replace with an alternative implementation for TLS protocol negotiation and cryptographic primitives\. The TLS interface can be swapped out without any changes required to the SSL interface\. See `iot_tls.h` in the Amazon FreeRTOS source code repository\.

The TLS interface is optional because you can choose to interface directly from SSL into a crypto library\. The interface is not used for MCU solutions that include a full\-stack offload implementation of TLS and network transport\.

For more information about porting the TLS interface, see [Porting the TLS Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tls.html) in the *Amazon FreeRTOS Porting Guide*\.
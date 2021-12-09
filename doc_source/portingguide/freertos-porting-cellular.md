# Porting the Cellular Interface library<a name="freertos-porting-cellular"></a>

FreeRTOS supports the AT commands of a TCP offloaded cellular abstraction Layer\. For more information, see the [Cellular Interface Library](https://freertos.org/cellular/index.html) and [Porting the Cellular Interface Library](https://freertos.org/cellular-porting-guide.html) on freertos\.org\.

## Prerequisites<a name="freertos-porting-cellular-prereqs"></a>

There is no direct dependency for the Cellular Interface library, however, in the FreeRTOS network stack, Ethernet, Wi\-Fi and cellular cannot co\-exist, so developers must choose one of them to integrate with the [Secure Sockets library](https://docs.aws.amazon.com/freertos/latest/userguide/secure-sockets.html)\.

**Note**  
If the cellular module is able to support TLS offload, or does not support AT commands, developers can implement their own cellular abstraction to integrate with the [Secure Sockets library](https://docs.aws.amazon.com/freertos/latest/userguide/secure-sockets.html)\.
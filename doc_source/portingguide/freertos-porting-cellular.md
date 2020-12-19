# Porting the Cellular library<a name="freertos-porting-cellular"></a>

FreeRTOS Labs now supports AT commands, TCP offloaded cellular abstraction Layer\. For more information, see [FreeRTOS Labs \- Cellular Libraries](https://freertos.org/cellular/index.html) and the [Porting Guide](https://freertos.org/cellular-porting-guide.html) for more information\.

**Note**  
The Cellular Library is in FreeRTOS Labs\. The libraries in the FreeRTOS Labs download are fully functional, but undergoing improvements to their implementation, documentation, and coding style\.

## Prerequisites<a name="freertos-porting-cellular-prereqs"></a>

There is no direct dependency for the Cellular Library, however, in the FreeRTOS network stack, Ethernet, Wi\-Fi and cellular cannot co\-exist, so developers must choose one of them to integrate with the [Secure Sockets library](https://docs.aws.amazon.com/freertos/latest/userguide/secure-sockets.html)\.

**Note**  
If the cellular module is able to support TLS offload, or does not support AT commands, developers can implement their own cellular abstraction to integrate with the [Secure Sockets library](https://docs.aws.amazon.com/freertos/latest/userguide/secure-sockets.html)\.
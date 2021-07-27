# coreHTTP library<a name="core-http"></a>

**HTTP C client library for small IoT devices \(MCU or small MPU\)**

## Introduction<a name="core-http-introduction"></a>

The coreHTTP library is a client implementation of a subset of the [HTTP/1\.1](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) standard\. The HTTP standard provides a stateless protocol that runs on top of TCP/IP and is often used in distributed, collaborative, hypertext information systems\.

The coreHTTP library implements a subset of the [HTTP/1\.1 ](https://tools.ietf.org/html/rfc2616) protocol standard\. This library has been optimized for a low memory footprint\. The library provides a fully synchronous API so applications can completely manage their concurrency\. It uses fixed buffers only, so that applications have complete control of their memory allocation strategy\.

The library is written in C and designed to be compliant with [ISO C90](https://en.wikipedia.org/wiki/ANSI_C#C90) and [MISRA C:2012](https://www.misra.org.uk/MISRAHome/MISRAC2012/tabid/196/Default.aspx)\. The library's only dependencies are the standard C library and [LTS version \(v12\.19\.1\) of the http\-parser](https://github.com/nodejs/node/tree/v12.19.1/deps/http_parser) from Node\.js\. The library has [proofs](https://www.cprover.org/cbmc/) showing safe memory use and no heap allocation, making it suitable for IoT microcontrollers, but also fully portable to other platforms\.

When using HTTP connections in IoT applications, we recommend that you use a secure transport interface, such as one that uses the TLS protocol as demonstrated in the   [coreHTTP mutual authentication demo](core-http-ma-demo.md)\.

This library can be freely used and is distributed under the [MIT open source license](https://freertos.org/a00114.html)\.


****  

| Code Size of coreHTTP \(example generated with GCC for ARM Cortex\-M\) | File | With \-O1 Optimization | With \-Os Optimization | 
| --- | --- | --- | --- | 
| core\_http\_client\.c | 3\.2K | 2\.6K | 
| http\_parser\.c \(http\-parser\) | 15\.7K | 13\.0K | 
| Total estimates | 18\.9K | 15\.6K | 
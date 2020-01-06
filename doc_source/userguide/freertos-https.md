# Amazon FreeRTOS HTTPS Client Library<a name="freertos-https"></a>

## Overview<a name="freertos-https-overview"></a>

You can use the Amazon FreeRTOS HTTPS Client library to create applications that interact with an HTTP server to send HTTP requests and receive HTTP responses over TLS\. The Amazon FreeRTOS HTTPS Client library implements the HTTP/1\.1 standard over TLS\.

The source files for the Amazon FreeRTOS HTTPS Client library are located in `<amazon-freertos>/libraries/csdk/standard/https`\.

## Dependencies and Requirements<a name="freertos-https-dependencies"></a>

The Amazon FreeRTOS HTTPS Client library has the following dependencies:
+ [Amazon FreeRTOS Linear Containers Library](lib-linear.md)\.
+ [Amazon FreeRTOS Logging Library](lib-logging.md) \(if the configuration parameter `AWS_IOT_HTTPS_LOG_LEVEL` is not set to `AWS_IOT_LOG_NONE`\)\.
+ [Amazon FreeRTOS Static Memory Library](lib-static.md) \(if static memory only\)\.
+ [Amazon FreeRTOS Task Pool Library](task-pool.md)\.
+ The platform layer that provides an interface to the operating system for thread management, clock functions, networking, and other platform\-level functionality\.
+ C standard library headers\.

The diagram below illustrates these dependencies\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/https-dependencies.png)

## Features<a name="freertos-https-features"></a>

The Amazon FreeRTOS HTTPS Client library has the following features:
+ Both fully asynchronous and synchronous \(blocking\) API functions\.
+ Application managed memory for internal context and HTTP formatted headers\.
+ Thread\-aware and parallelized connections\.

## API Reference<a name="freertos-https-api"></a>

For a full API reference, see the [HTTPS Client API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/https/index.html)\.
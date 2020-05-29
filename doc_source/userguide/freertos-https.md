# HTTPS client library<a name="freertos-https"></a>

## Overview<a name="freertos-https-overview"></a>

You can use the FreeRTOS HTTPS Client library to create applications that interact with an HTTP server to send HTTP requests and receive HTTP responses over TLS\. The FreeRTOS HTTPS Client library implements the HTTP/1\.1 standard over TLS\.

The source files for the FreeRTOS HTTPS Client library are located in `freertos/libraries/csdk/standard/https`\.

## Dependencies and requirements<a name="freertos-https-dependencies"></a>

The FreeRTOS HTTPS Client library has the following dependencies:
+ [Linear Containers library](lib-linear.md)\.
+ [Logging library](lib-logging.md) \(if the configuration parameter `AWS_IOT_HTTPS_LOG_LEVEL` is not set to `AWS_IOT_LOG_NONE`\)\.
+ [Static Memory library](lib-static.md) \(if Static Memory only\)\.
+ [Task Pool library](task-pool.md)\.
+ The platform layer that provides an interface to the operating system for thread management, clock functions, networking, and other platform\-level functionality\.
+ C standard library headers\.

The diagram below illustrates these dependencies\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/https-dependencies.png)

## Features<a name="freertos-https-features"></a>

The FreeRTOS HTTPS Client library has the following features:
+ Both fully asynchronous and synchronous \(blocking\) API functions\.
+ Application managed memory for internal context and HTTP formatted headers\.
+ Thread\-aware and parallelized connections\.

## API reference<a name="freertos-https-api"></a>

For a full API reference, see the [HTTPS Client API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/https/index.html)\.
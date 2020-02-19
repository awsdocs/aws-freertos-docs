# HTTPS Demo Applications<a name="https-demo"></a>

## Overview<a name="https-demo-overview"></a>

For examples using the FreeRTOS HTTPS Client library, see the HTTPS Client demo applications defined in `iot_demo_https_s3_download_async.c` and `iot_demo_https_s3_download_sync.c`\. 

The HTTPS Client demos show how to download a file from Amazon S3 using a pre\-signed URL\. The file is downloaded incrementally using HTTP Partial Content headers\. The byte ranges for the size of the response body buffer are specified in each incremental request\. The HTTPS Client library is a generic library that can be used to download files from other webservers as well\. Please note that not all HTTP servers support a Partial Content download with a byte range\. 

To use the HTTPS Client demos, you must also set values for the following constants in the `<freertos>/demos/include/aws_clientcredential_keys.h` file:

`keyCLIENT_CERTIFICATE_PEM`  
The certificate PEM needed for a TLS connection\.

`keyCLIENT_PRIVATE_KEY_PEM`  
The private key PEM needed for a TLS connection\.Configuration Parameters

These configuration parameters apply to the HTTPS Client demo and need to be defined\.

`IOT_DEMO_HTTPS_PRESIGNED_GET_URL`  
The pre\-signed URL for a GET request to Amazon S3 for a specific object\. This must be of the form:   

```
https://my-bucket.s3.amazonaws.com/object-key.txt?AWSAccessKeyId=AKIAIOSFODNN7EXAMPLE&Expires=1560555644&Signature=SomeHash12345UrlABcdEFgfIjK%3D
```
Please see `<freertos>/demos/https/README.md` for instructions on generating a pre\-signed URL using the Python script in the same folder\.

These configuration parameters apply to the HTTPS Client demo and do not need to be defined, they are for more customization options\.

`IOT_DEMO_HTTPS_PORT`  
The HTTPS server TCP port to connect to\. The default is 443\.

`IOT_DEMO_HTTPS_TRUSTED_ROOT`  
The trusted ROOT CA to connect to the HTTPS server\. The HTTPS server is defined by the host name in the `IOT_DEMO_HTTPS_PRESIGNED_GET_URL`\. The default is the Baltimore Cybertrust root certificate authority\. 

See [ HTTPS Client API Reference Demo Configuration](https://docs.aws.amazon.com/freertos/latest/lib-ref/https/https_demo_config.html) for other configurations\.

## Usage Instructions<a name="https-demo-usage"></a>

Please see the [ HTTPS Client API Reference Demo Usage Instructions](https://docs.aws.amazon.com/freertos/latest/lib-ref/https/https_demo_usage.html) for more information and output examples\. 
# Setting up an echo server<a name="afr-echo-server"></a>

The `freertos/tools/echo_server/` directory has the source code for a Go\-based echo server that you can use to test TCP on FreeRTOS\. You can find the TCP tests in the `freertos/libraries/abstractions/secure_sockets/test/iot_test_tcp.c` file\. Follow the instructions in this section to set up and run the echo server\. 

## Prerequisites<a name="afr-echo-server-prerequisites"></a>

To run the TLS echo server, you must install the following:
+ Go – You can download the latest version from [ golang\.org](https://golang.org/dl/)\.
+ OpenSSL – For a Linux source code download, see [OpenSSL\.org](https://www.openssl.org/source/)\. You can also use a package manager to install OpenSSL for Linux and macOS\.

## Create credentials<a name="afr-echo-server-credentials"></a>

After you finish the prerequisites, you must enter the following commands to create your credentials\.

**Server**  
The following `openssl` command generates a self\-signed server certificate\.  

```
openssl req -newkey rsa:2048 -nodes -x509 -sha256 -out certs/server.pem -keyout certs/server.key -days 365 -subj "/C=US/ST=WA/L=Place/O=YourCompany/OU=IT/CN=www.your-company-website.com/emailAddress=yourEmail@your-company-website.com"
```

**Client**  
The following `openssl` commands generate a client certificate\.  

```
openssl genrsa -out certs/client.key 2048
```

```
openssl req -new -key certs/client.key -out certs/client.csr -subj "/C=US/ST=WA/L=Place/O=YourCompany/OU=IT/CN=www.your-company-website.com/emailAddress=yourEmail@your-company-website.com"
```

```
openssl x509 -req -in certs/client.csr -CA certs/server.pem -CAkey certs/server.key -CAcreateserial -out certs/client.pem -days 365 -sha256
```

## Directory structure<a name="afr-echo-server-folder-struct"></a>

By default, certificates and keys are stored in a directory named `certs` that is located on a relative path specified in the configuration file, `config.json`\. If you want to move your credentials to a different directory, you can update this directory location in the configuration file\.

You can find the source code for the echo server in the `echo_server.go` file\.

## Server configuration<a name="afr-echo-server-config"></a>

The echo server reads a JSON based configuration file\. The default location for this JSON file is `./config.json`\. To override this, specify the location of the JSON with the `-config` flag\.

The JSON file contains the following options:

**server\-port**  
Specify the port on which to open a socket\.

**server\-certificate\-location**  
The relative or absolute path to the server certificate generated in [Create credentials](#afr-echo-server-credentials)\.

**secure\-connection**  
Enable this option to have the echo server use TLS\. You must first [Create credentials](#afr-echo-server-credentials)\.

**logging**  
Enable this option to output all log messages received to a file\.

**verbose**  
Enable this option to output the contents of the message sent to the echo server\.

**server\-key\-location**  
The relative or absolute path to the server key generated in [Create credentials](#afr-echo-server-credentials)\.

**Example configuration**

```
{
    "verbose": false,
    "logging": false,
    "secure-connection": false,
    "server-port": "9000",
    "server-certificate-location": "./certs/server.pem",
    "server-key-location": "./certs/server.pem"
}
```

## Run the echo server from the command line<a name="afr-echo-server-run"></a>

Enter the following commands to run the echo server\.

```
go run echo_server.go
```

Enter the following command to run with a custom config location\.

```
go run echo_server.go -config=config_file_path
```

If you want to run the unsecure and secure TCP tests at the same time, you must start both a secure and an unsecure echo server\. To do this, create a second, secure configuration file, and pass its location to the second instance of the echo server using the `-config` flag\. Remember to also specify a different TCP port in the second configuration file\.

## Client device configuration<a name="afr-echo-server-device-config"></a>

Before you run the TCP tests on your device, we recommend that you read [Getting Started with FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html) in the *FreeRTOS User Guide*\.

After you complete the steps in [Create credentials](#afr-echo-server-credentials), you should have the following files:
+ `certs/server.pem`
+ `certs/server.key`
+ `certs/client.pem`
+ `certs/client.key`
+ `certs/client.csr`
+ `certs/server.srl`

Make the following changes to these files:

 `freertos/tests/include/aws_clientcredential.h`   
+ Define the broker endpoint\.
+ Define the thing name\.
+ Define access to Wi\-Fi \(if not on Ethernet\)\.

 `freertos/tests/include/aws_clientcredential_keys.h`   
+ Set `keyCLIENT_CERTIFICATE_PEM` to the contents of `certs/client.pem`\.
+ Leave `keyJITR_DEVICE_CERTIFICATE_AUTHORITY_PEM` as `NULL`\.
+ Set `keyCLIENT_PRIVATE_KEY_PEM` to the contents of `certs/client.key`\.
+ For more information, see [ Configuring the FreeRTOS demos](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-configure.html)\.

 `freertos/tests/include/aws_test_tcp.h`   
+ Set `tcptestECHO_HOST_ROOT_CA` to the contents of `certs/server.pem`\.
+ Set the IP address and the port of the echo server:
  + `tcptestECHO_SERVER_ADDR[0-3]`
  + `tcptestECHO_PORT`
+ Set the IP address and the port of the secure echo server:
  + `tcptestECHO_SERVER_TLS_ADDR0[0-3]`
  + `tcptestECHO_PORT_TLS`
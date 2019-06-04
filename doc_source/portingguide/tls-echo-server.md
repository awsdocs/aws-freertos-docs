# Setting Up the TLS Echo Server<a name="tls-echo-server"></a>

A TLS echo server is defined in `<amazon-freertos>/tools/echo_server/tls_echo_server.go`\. Follow these instructions to set up and run the TLS echo server\.

## Prerequisites<a name="tls-echo-server-prerequisites"></a>

To run the TLS echo server, you need to install the following:
+ Go\.

  You can download the latest version from [golang\.org](https://golang.org/dl/)\.
+ OpenSSL\.

  For a Linux source code download, see [OpenSSL\.org](https://www.openssl.org/source/)\. You can also use a package manager to install OpenSSL for Linux and macOS\.

## Setting Up the Server<a name="tls-echo-server-config"></a>

1. Copy `<amazon-freertos>/tools/echo_server/tls_echo_server.go` to a directory of your choice \(for example, `<echo_dir>`\)\.

1. Under `<echo_dir>`, create a subfolder named `certs`\.

1. Generate a TLS server self\-signed certificate and private key\. For OpenSSL commands to generate a self\-signed server certificate and private key, see `<amazon-freertos>/tools/echo_server/readme-gencert.txt`\.

1. Copy the self\-signed certificate and private key `.pem` files to the `certs` directory\.

1. Run the following command from the `<echo_dir>` directory to start the TLS server:

   ```
   go run tls_echo_server.go
   ```

   The server listens on port `9000` by default\. To change this port, open `tls_echo_server.go`, and redefine the `sEchoPort` string to the port number that you want\.
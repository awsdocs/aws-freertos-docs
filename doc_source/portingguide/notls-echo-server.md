# Setting Up the Echo Server \(Without TLS\)<a name="notls-echo-server"></a>

An unsecured echo server is defined in `<amazon-freertos>/tools/echo_server/echo_server.go`\. Follow these instructions to set up and run the echo server\.

## Prerequisites<a name="notls-echo-server-prerequisites"></a>

To run the echo server, you need the latest version of Go\. You can download the latest version from [golang\.org](https://golang.org/dl/)\.

## Setting Up the Server<a name="notls-echo-server-config"></a>

1. Copy `<amazon-freertos>/tools/echo_server/echo_server.go` to a directory of your choice \(for example, `<echo_dir>`\)\.

1. Run the following command from the `<echo_dir>` directory to start the server:

   ```
   go run echo_server.go
   ```

   The server listens on port `9001` by default\. To change this port, open `echo_server.go`, and redefine the `sUnSecureEchoPort` string to the port number that you want\.
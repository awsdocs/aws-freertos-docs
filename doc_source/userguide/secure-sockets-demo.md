# Secure Sockets echo client demo<a name="secure-sockets-demo"></a>

The following example uses a single RTOS task\. The source code for this example can be found at `demos/tcp/aws_tcp_echo_client_single_task.c`\.

Before you begin, verify that you have downloaded FreeRTOS to your microcontroller and built and run the FreeRTOS demo projects\. You can clone or download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. 

**To run the demo**

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.  
The TCP server and client demos are currently not supported on the Cypress CYW943907AEVAL1F and CYW954907AEVAL1F Development Kits\.

1. Follow the instructions in [Setting Up the TLS Echo Server](https://docs.aws.amazon.com/freertos/latest/portingguide/tls-echo-server.html) in the FreeRTOS Porting Guide\.

   A TLS echo server should be running and listening on the port 9000\.

   During the setup, you should have generated four files:
   + `client.pem` \(client certificate\)
   + `client.key` \(client private key\)
   + `server.pem` \(server certificate\)
   + `server.key` \(server private key\)

1. Use the tool `tools/certificate_configuration/CertificateConfigurator.html` to copy the client certificate \(`client.pem`\) and client private key \(`client.key`\) to `aws_clientcredential_keys.h`\.

1. Open the `FreeRTOSConfig.h` file\.

1. Set the `configECHO_SERVER_ADDR0`, `configECHO_SERVER_ADDR1`, `configECHO_SERVER_ADDR2`, and `configECHO_SERVER_ADDR3` variables to the four integers that make up the IP address where the TLS Echo Server is running\.

1. Set the `configTCP_ECHO_CLIENT_PORT` variable to `9000`, the port where the TLS Echo Server is listening\.

1. Set the `configTCP_ECHO_TASKS_SINGLE_TASK_TLS_ENABLED` variable to `1`\.

1. Use the tool `tools/certificate_configuration/PEMfileToCString.html` to copy the server certificate \(`server.pem`\) to `cTlsECHO_SERVER_CERTIFICATE_PEM` in the file `aws_tcp_echo_client_single_task.c`\.

1.  Open `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_TCP_ECHO_CLIENT_DEMO_ENABLED`\.

The microcontroller and the TLS Echo Server should be on the same network\. When the demo starts \(`main.c`\), you should see a log message that reads Received correct string from echo server\.
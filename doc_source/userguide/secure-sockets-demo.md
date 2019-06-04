# Secure Sockets Echo Client Demo<a name="secure-sockets-demo"></a>

The following example uses a single RTOS task\. The source code for this example can be found at [demos/common/tcp/aws\_tcp\_echo\_client\_single\_task\.c](https://github.com/aws/amazon-freertos/blob/master/demos/common/tcp/aws_tcp_echo_client_single_task.c)\.

Before you begin, verify that you have downloaded Amazon FreeRTOS to your microcontroller and built and run the Amazon FreeRTOS demo projects\. You can download Amazon FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. For more information about setting up an Amazon FreeRTOS\-qualfied board, see [Getting Started with Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html)\.

**To run the demo**

**Note**  
The TCP server and client demos are currently not supported on the Cypress CYW943907AEVAL1F and CYW954907AEVAL1F Development Kits\.

1. Follow the instructions in the "TLS Server Setup" section of the [Amazon FreeRTOS Qualification Developer Guide](https://github.com/aws/amazon-freertos/blob/master/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf) to set up a TLS Echo Server\.

   By the end of step 6, the TLS Echo Server should be running and listening on the port 9000\. You do not need to complete steps 7, 8, and 9\.

   During the setup, you should have generated four files:
   + `client.pem` \(client certificate\)
   + `client.key` \(client private key\)
   + `server.pem` \(server certificate\)
   + `server.key` \(server private key\)

1. Use the tool `tools\certificate_configuration\CertificateConfigurator.html` to copy the client certificate \(`client.pem`\) and client private key \(`client.key`\) to `aws_clientcredential_keys.h`\.

1. Open the `FreeRTOSConfig.h` file\.

1. Set the `configECHO_SERVER_ADDR0`, `configECHO_SERVER_ADDR1`, `configECHO_SERVER_ADDR2`, and `configECHO_SERVER_ADDR3` variables to the four integers that make up the IP address where the TLS Echo Server is running\.

1. Set the `configTCP_ECHO_CLIENT_PORT` variable to `9000`, the port where the TLS Echo Server is listening\.

1. Set the `configTCP_ECHO_TASKS_SINGLE_TASK_TLS_ENABLED` variable to `1`\.

1. Use the tool `tools\certificate_configuration\PEMfiileToCString.html` to copy the server certificate \(`server.pem`\) to `cTlsECHO_SERVER_CERTIFICATE_PEM` in the file `aws_tcp_echo_client_single_task.c`\.

1. In `demos/common/demo_runneraws_demo_runner.c`, switch the demo function to `vStartTCPEchoClientTasks_SingleTasks()`:

   ```
   //extern void vStartMQTTEchoDemo( void );
   extern void *vStartTCPEchoClientTasks_SingleTasks*( void );
   
   /**
    * @brief Runs demos in the system.
    */
   void DEMO_RUNNER_RunDemos( void )
   {
       //vStartMQTTEchoDemo();
       vStartTCPEchoClientTasks_SingleTasks();
   }
   ```

The microcontroller and the TLS Echo Server should be on the same network\. When the demo starts \(`main.c`\), you should see a log message that reads Received correct string from echo server\.
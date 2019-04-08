# AWS IoT Greengrass Discovery Demo Application<a name="gg-demo"></a>

Before you run the AWS IoT Greengrass Discovery demo for Amazon FreeRTOS, you need to set up AWS, AWS IoT Greengrass, and AWS IoT\. To set up AWS, follow the instructions at [Setting Up Your AWS Account and Permissions](freertos-account-and-permissions.md)\. To set up AWS IoT Greengrass, you need to create a Greengrass group and then add a Greengrass core\. For more information about setting up AWS IoT Greengrass, see [Getting Started with AWS IoT Greengrass](http://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html)\. 

After you set up AWS and AWS IoT Greengrass, you need to configure some additional permissions for AWS IoT Greengrass\.

**To set up AWS IoT Greengrass permissions**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. From the navigation pane, choose **Roles**, and then find and choose **Greengrass\_ServiceRole**\.

1. Choose **Attach policies**, select **AmazonS3FullAccess** and **AWSIoTFullAccess**, and then choose **Attach policy**\.

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Greengrass**, choose **Groups**, and then choose the Greengrass group that you previously created\.

1. Choose **Settings**, and then choose **Add role**\.

1. Choose **Greengrass\_ServiceRole**, and then choose **Save**\.

You can use the **Quick Connect** workflow in the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) to quickly connect your board to the AWS IoT and run the demo\. Amazon FreeRTOS configurations are currently not available for the following boards:
+ Cypress CYW943907AEVAL1F Development Kit
+ Cypress CYW954907AEVAL1F Development Kit
+ Espressif ESP\-WROVER\-KIT
+ Espressif ESP32\-DevKitC
+ Nordic nRF52840\-DK

You can also connect your board to AWS IoT and configure your Amazon FreeRTOS demo manually\.

1. [Registering Your MCU Board with AWS IoT](get-started-freertos-thing.md)

   After you register your board, you need to create and attach a new Greengrass policy to the device's certificate\.

**To create a new AWS IoT Greengrass policy**

   1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

   1. In the navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\.

   1. Enter a name to identify your policy\.

   1. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window:

      ```
      {
        "Effect": "Allow",
        "Action": [
          "greengrass:*"
        ],
        "Resource": "*"
      }
      ```

      This policy grants AWS IoT Greengrass permissions to all resources\.

   1. Choose **Create**\.

**To attach the AWS IoT Greengrass policy to your device's certificate**

   1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

   1. In the navigation pane, choose **Manage**, choose **Things**, and then choose the thing that you previously created\.

   1. Choose **Security**, and then choose the certificate attached to your device\.

   1. Choose **Policies**, choose **Actions**, and then choose **Attach Policy**\.

   1. Find and choose the Greengrass policy that you created earlier, and then choose **Attach**\.

1. [Downloading Amazon FreeRTOS](freertos-download.md)
**Note**  
If you are downloading Amazon FreeRTOS from the Amazon FreeRTOS console, choose **Connect to AWS IoT Greengrass\- *Platform*** instead of **Connect to AWS IoT\- *Platform***\.

1. [Configuring the Amazon FreeRTOS Demos](freertos-configure.md)

   If you downloaded Amazon FreeRTOS from GitHub, you need to open `<BASE_FOLDER>/demos/common/demo_runner/aws_demo_runner.c`, comment out `extern void vStartMQTTEchoDemo( void );` and `vStartMQTTEchoDemo();`, and uncomment `extern void vStartGreenGrassDiscoveryTask( void );` and `vStartGreenGrassDiscoveryTask();`\.

After you set up AWS IoT and AWS IoT Greengrass, and after you download and configure Amazon FreeRTOS, you can build and run the Greengrass demo in your IDE\. To set up your board's hardware and software development environment, follow the instructions in the [Board\-specific Getting Started Guides](freertos-getting-started.md#getting-started-guides)\.

The Greengrass demo publishes a series of messages to the Greengrass core, and to the AWS IoT MQTT client\. To view the messages in the AWS IoT MQTT client, open the [AWS IoT console](https://console.aws.amazon.com/iotv2/), choose **Test**, and then add a subscription to `freertos/demos/ggd`\.

In the MQTT client, you should see the following strings:

```
Message from Thing to Greengrass Core: Hello world msg #1!
Message from Thing to Greengrass Core: Hello world msg #0!
Message from Thing to Greengrass Core: Address of Greengrass Core found! <123456789012>.<us-west-2>.compute.amazonaws.com
```
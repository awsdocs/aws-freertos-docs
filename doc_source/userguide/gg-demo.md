# AWS IoT Greengrass discovery demo application<a name="gg-demo"></a>

Before you run the AWS IoT Greengrass Discovery demo for FreeRTOS, you need to set up AWS, AWS IoT Greengrass, and AWS IoT\. To set up AWS, follow the instructions at [Setting up your AWS account and permissions](freertos-prereqs.md#freertos-account-and-permissions)\. To set up AWS IoT Greengrass, you need to create a Greengrass group and then add a Greengrass core\. For more information about setting up AWS IoT Greengrass, see [Getting Started with AWS IoT Greengrass](https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html)\. 

After you set up AWS and AWS IoT Greengrass, you need to configure some additional permissions for AWS IoT Greengrass\.

**To set up AWS IoT Greengrass permissions**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. From the navigation pane, choose **Roles**, and then find and choose **Greengrass\_ServiceRole**\.

1. Choose **Attach policies**, select **AmazonS3FullAccess** and **AWSIoTFullAccess**, and then choose **Attach policy**\.

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Greengrass**, choose **Groups**, and then choose the Greengrass group that you previously created\.

1. Choose **Settings**, and then choose **Add role**\.

1. Choose **Greengrass\_ServiceRole**, and then choose **Save**\.

You can use the **Quick Connect** workflow in the [FreeRTOS console](https://console.aws.amazon.com/freertos) to quickly connect your board to AWS IoT and run the demo\. FreeRTOS configurations are currently not available for the following boards:
+ Cypress CYW943907AEVAL1F Development Kit
+ Cypress CYW954907AEVAL1F Development Kit
+ Espressif ESP\-WROVER\-KIT
+ Espressif ESP32\-DevKitC
+ Nordic nRF52840\-DK

You can also connect your board to AWS IoT and configure your FreeRTOS demo manually\.

1. [Registering your MCU board with AWS IoT](freertos-prereqs.md#get-started-freertos-thing)

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

1. [Downloading FreeRTOS](freertos-prereqs.md#freertos-download)
**Note**  
If you are downloading FreeRTOS from the FreeRTOS console, choose **Connect to AWS IoT Greengrass\- *Platform*** instead of **Connect to AWS IoT\- *Platform***\.

1. [Configuring the FreeRTOS demos](freertos-prereqs.md#freertos-configure)\.

   Open `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_GREENGRASS_DISCOVERY_DEMO_ENABLED`\.

After you set up AWS IoT and AWS IoT Greengrass, and after you download and configure FreeRTOS, you can build, flash, and run the Greengrass demo on your device\. To set up your board's hardware and software development environment, follow the instructions in the [Board\-specific getting started guides](getting-started-guides.md)\.

The Greengrass demo publishes a series of messages to the Greengrass core, and to the AWS IoT MQTT client\. To view the messages in the AWS IoT MQTT client, open the [AWS IoT console](https://console.aws.amazon.com/iotv2/), choose **Test**, and then add a subscription to `freertos/demos/ggd`\.

In the MQTT client, you should see the following strings:

```
Message from Thing to Greengrass Core: Hello world msg #1!
Message from Thing to Greengrass Core: Hello world msg #0!
Message from Thing to Greengrass Core: Address of Greengrass Core found! 123456789012.us-west-2.compute.amazonaws.com
```

## Using an Amazon EC2 instance<a name="gg-demo-ec2"></a>

**If you are working with an Amazon EC2 instance**

1. Find the Public DNS \(IPv4\) associated with your Amazon EC2 instance— go to the Amazon EC2 console, and in the left navigation panel, choose **Instances**\. Choose your Amazon EC2 instance, and then choose the **Description** panel\. Look for the entry for the **Public DNS \(IPv4\)** and make a note of it\.

1. Find the entry for **Security groups** and choose the security group attached to your Amazon EC2 instance\.

1. Choose the **Inbound rules** tab then choose **Edit inbound rules** and add the following rules\.  
**Inbound rules**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/userguide/gg-demo.html)

1. In the AWS IoT console choose **Greengrass**, then **Groups**, and choose the Greengrass group that you previously created\. Choose **Settings**\. Change the **Local connection detection** to **Manually manage connection information**\.

1. In the navigation pane, choose **Cores** then select your group core\.

1. Choose **Connectivity** and make sure you have only one core endpoint \(delete all of the rest\) and that it is not an IP address \(because it is subject to change\)\. The best option is to use the Public DNS \(IPv4\) that you noted in the first step\.

1. Add the FreeRTOS IoT thing you created to the GG group\.

   1. Choose the back arrow to return to the AWS IoT Greengrass group page\. In the navigation pane, choose **Devices** then choose **Add Device**\.

   1. Choose **Select an IoT Thing**\. Choose your device then choose **Finish**\.

1. Add the necessary subscriptions— in the **Greengrass Group** page, choose **Subscriptions** then choose **Add Subscription** and enter information as shown here\.  
**Subscriptions**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/userguide/gg-demo.html)

1. Start a deployment of your AWS IoT Greengrass group and make sure that the deployment is successful\. You should now be able to successfully run the AWS IoT Greengrass discovery demo\.
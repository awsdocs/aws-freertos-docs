# AWS IoT Device Shadow Demo Application<a name="shadow-demo"></a>

Amazon FreeRTOS includes a demo application that demonstrates how to programmatically update and respond to changes in an AWS IoT Device Shadow\. This demo application is defined in `Amazon FreeRTOS/demos/shadow/aws_iot_demo_shadow.c`\. The device in this scenario is a light bulb whose color can be set to red or green\.

The demo application creates three tasks:
+ A main demo task that calls `prvShadowMainTask`\.
+ A device update task that calls `prvUpdateTask`\.
+ A number of shadow update tasks that call `prvShadowUpdateTasks`\.

`prvShadowMainTask` initializes the Device Shadow client and initiates an MQTT connection to AWS IoT with the client credentials specified in `demos/include/aws_clientcredential.h`\. The information specified in `aws_clientcredential.h`, including your your device's AWS IoT thing name and the MQTT broker endpoint and port, must be correct for the application to successfully connect to the AWS cloud\.

After the MQTT connection is established, the application creates the device update task\. Finally, it creates shadow update tasks and then terminates\. The `democonfigSHADOW_DEMO_NUM_TASKS` constant defined in `aws_iot_demo_shadow.c` controls the number of shadow update tasks created\.

`prvShadowUpdateTasks` generates an initial thing shadow document and updates the shadow with the document\. It then goes into an infinite loop that periodically updates the thing shadow's desired state, requesting that the light bulb change its color \(from red to green to red\)\.

`prvUpdateTask` responds to changes in the shadow's desired state\. When the desired state changes, this task updates the reported state of the shadow to reflect the new desired state\.

Before you can run the Device Shadow demo, you must complete the getting started [First Steps](freertos-prereqs.md) to set up AWS IoT and Amazon FreeRTOS so your device can communicate with the AWS cloud\.

After you set up AWS IoT and Amazon FreeRTOS, do the following:

1. Add the following policy to your device certificate:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "iot:Connect",
         "Resource": "arn:aws:iot:us-west-2:123456789012:client/<yourClientId>"
       },
       {
         "Effect": "Allow",
         "Action": "iot:Subscribe",
         "Resource": "arn:aws:iot:us-west-2:123456789012:topicfilter/$aws/things/thingName/shadow/*"
       },
       {
         "Effect": "Allow",
         "Action": "iot:Receive",
         "Resource":
         "arn:aws:iot:us-west-2:123456789012:topic/$aws/things/thingName/shadow/*"
       },
       {
         "Effect": "Allow",
         "Action": "iot:Publish",
         "Resource":
         "arn:aws:iot:us-west-2:123456789012:topic/$aws/things/thingName/shadow/*"
       }
     ]
   }
   ```

1.  Open `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_SHADOW_DEMO_ENABLED`\.

1. Build, flash, and run Amazon FreeRTOS to your device\.

You can use the AWS IoT console to view your device's shadow and confirm that its desired and reported states change periodically\.

1. In the AWS IoT console, from the left navigation pane, choose **Manage**\. 

1. Under **Manage**, choose **Things**, and then choose the thing whose shadow you want to view\.

1. On the thing detail page, from the left navigation pane, choose **Shadow** to display the thing shadow\.

For more information about how devices and shadows interact, see [Device Shadow Service Data Flow](https://docs.aws.amazon.com/iot/latest/developerguide/device-shadow-data-flow.html)\.
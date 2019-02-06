# AWS IoT Device Shadow Demo Application<a name="shadow-demo"></a>

The device shadow example demonstrates how to programmatically update and respond to changes in a device shadow\. The device in this scenario is a light bulb whose color can be set to red or green\. The device shadow example app is located in the `AmazonFreeRTOS/demos/common/shadow` directory\. This example creates three tasks:
+ A main demo task that calls `prvShadowMainTask`\.
+ A device update task that calls `prvUpdateTask`\.
+ A number of shadow update tasks that call `prvShadowUpdateTasks`\.

`prvShadowMainTask` initializes the device shadow client and initiates an MQTT connection to AWS IoT\. It then creates the device update task\. Finally, it creates shadow update tasks and then terminates\. The `democonfigSHADOW_DEMO_NUM_TASKS` constant defined in `AmazonFreeRTOS/demos/common/shadow/aws_shadow_lightbulb_on_off.c` controls the number of shadow update tasks created\.

`prvShadowUpdateTasks` generates an initial thing shadow document and updates the device shadow with the document\. It then goes into an infinite loop that periodically updates the thing shadow's desired state, requesting the light bulb change its color \(from red to green to red\)\.

`prvUpdateTask` responds to changes in the device shadow's desired state\. When the desired state changes, this task updates the reported state of the device shadow to reflect the new desired state\.

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

1. Uncomment the declaration of and call to `vStartShadowDemoTasks` in `aws_demo_runner.c`\. This function creates a task that runs the `prvShadowMainTask` function\.

You can use the AWS IoT console to view your device's shadow and confirm that its desired and reported states change periodically\.

1. In the AWS IoT console, from the left navigation pane, choose **Manage**\. 

1. Under **Manage**, choose **Things**, and then choose the thing whose shadow you want to view\.

1. On the thing detail page, from the left navigation pane, choose **Shadow** to display the thing shadow\. 

For more information about how devices and shadows interact, see [Device Shadow Data Flow](http://docs.aws.amazon.com/iot/latest/developerguide/thing-shadow-data-flow.html)\.
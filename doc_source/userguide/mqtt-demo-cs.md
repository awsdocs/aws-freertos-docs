# coreMQTT connection sharing demo<a name="mqtt-demo-cs"></a>

## Introduction<a name="mqtt-demo-cs-introduction"></a>

The coreMQTT connection sharing demo project shows you how to use a multithreaded application to establish a connection to the AWS MQTT broker using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server and client\-authenticated TLS connection, and demonstrates the subscribe\-publish workflow of MQTT at the [ QoS 1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/errata01/os/mqtt-v3.1.1-errata01-os-complete.html#_Toc442180914) level\. The demo subscribes to a topic filter, publishes to topics that match the filter, and then waits to receive those messages back from the server at the QoS 1 level\. This cycle of publishing to the broker and receiving the same message back from the broker is repeated indefinitely\. Messages in this demo are sent at QoS 1, which guarantees at least one delivery according to the MQTT specification\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

This demo uses a thread safe queue to hold commands to interact with the MQTT API\. There are four tasks to take note of in this demo\.
+ A command \(main\) task takes commands from the command queue and processes them\. The other tasks place commands in this queue to be processed\. This task enters a loop, during which it processes the commands\. If a termination command is received, this task will break out of the loop\.
+ A synchronous publisher task creates a series of publish operations and pushes them to the command queue\. These operations are then run by the command task\. This task uses synchronous publishing, which means that this task will wait for each publish operation to complete before scheduling the next one\.
+ An asynchronous publisher task creates a series of publish operations and pushes them to the command queue\. These operations are then run by the command task\. The difference between this task and the previous one is that it will not wait for a publish operation to complete before it schedules the next one\. It checks the status of each publish operation after all of its publish operations have been added to the queue\. Note that the distinction between synchronous and asynchronous publishing is only in the behavior of these tasks\. There are no differences in the actual publish commands\.
+ A subscriber task creates an MQTT subscription to a topic filter that matches the topics of all messages which the two publisher tasks publish\. This task enters a loop and waits to receive back the messages that were published by the other tasks\.

Tasks can have queues to hold received messages\. The command task will push incoming messages to the queue of any task that is subscribed to the incoming topic\.

This demo uses a TLS connection with mutual authentication to connect to AWS\. If the network unexpectedly disconnects during the demo, then the client attempts to reconnect using exponential backoff logic\. If the client successfully reconnects, but the broker can't resume the prior session, then the client will resubscribe to the same topics as the previous session\.

### Single threaded vs multithreaded<a name="mqtt-demo-cs-single-vs-multi"></a>

There are two coreMQTT usage models, single threaded and multithreaded \(multitasking\)\. This demo shows you how to create your own multithreading scheme\. There is also another multithreaded example that runs the MQTT protocol in the background within an agent \(or daemon\) task\. For more information, see [MWTT Agent and Demos using coreMQTT](https://freertos.org/mqtt/mqtt-agent-demo.html)\. If you run the MQTT protocol in an agent task, then it isn't necessary to explicitly manage any MQTT state or call the `MQTT_ProcessLoop` function\. Also, if you use an agent task, multiple application tasks can share a single MQTT connection without the need for synchronization primitives, such as mutexes\.

## Source code<a name="mqtt-demo-cs-source-code"></a>

The demo source file is named `mqtt_demo_connection_sharing.c` and can be found in the `freertos/demos/coreMQTT/` directory and the [ GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c) website\.

## Functionality<a name="mqtt-demo-cs-functionality"></a>

This demo creates four tasks in total: three that request MQTT API calls, and one that processes those requests, which is the primary task\. In this demo, the primary task enters a loop that creates the three subtasks, calls the processing loop, and cleans up afterwards\. The primary task creates a single MQTT connection to the broker that is shared among the subtasks\. Two of the subtasks publish messages to the broker, and the third receives the messages back, using an MQTT subscription to a topic filter that matches all of the topics of the messages published\.

## Typedefs<a name="mqtt-demo-cs-typedefs"></a>

The demo defines the following structures, enums, and function pointers\.

Commands  
Rather than making the MQTT API calls directly, the tasks use `Command_t` structures to create commands that instruct the main task to call the appropriate API operation for them\. Commands have the following types:  
+ `PROCESSLOOP`
+ `PUBLISH`
+ `SUBSCRIBE`
+ `UNSUBSCRIBE`
+ `PING`
+ `DISCONNECT`
+ `RECONNECT`
+ `TERMINATE`
The `TERMINATE` command doesn't have a corresponding MQTT API operation\. It's used in the demo to instruct the main task to stop processing commands and begin cleanup operations\. Because some additional information, for example, publish or subscribe information, is required for some MQTT commands such as `MQTT_Publish`, `MQTT_Subscribe`, and `MQTT_Unsubscribe`, we use the `CommandContext_t` field\. This field is required for these three commands, and it's optional for the others\.  
Because this context is required for these commands, don't change this value once the command has been placed in the queue, until the command completes\. When a command completes, an optional callback can be invoked\. In this demo, we use a callback that creates a task notification to inform the calling task that the command has completed\. For MQTT operations that require acknowledgments \(subscribes, unsubscribes, and publishes with QoS greater than 0\), the command is considered completed once the acknowledgment has been received\. Otherwise, the command is completed once the corresponding MQTT API call has returned\.  
The following definitions can be found in the `mqtt_demo_connection_sharing.c` file:  
+ [ Command\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L355-L363) struct
+ [ CommandContext\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L329-L348) struct
+ [ CommandType\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L314-L327) enum
+ [ CommandCallback\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L350-L353) function pointer

Acknowledgments  
Because some MQTT operations require an acknowledgment, they use an array of [ AckInfo\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L365-L372) that contains the packet identifier of the expected acknowledgment, and the original command that is expecting it, so that its completion callback can be invoked\.

Subscriptions  
This demo can track subscriptions for each task\. To do so, each task that requests a subscription must provide a message queue where it will receive back the published messages \([SubscriptionElement\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L374-L386)\)\. Multiple tasks can subscribe to the same topic filter, because they're expected to use separate response queues\.

Received published messages  
Because tasks run in parallel with the main task, it would be difficult and time consuming for the main task to have to wait for each subscribed task to read a received published message\. Therefore, each message received is copied to the response queue of any task that is subscribed to the published message's topic \([PublishElement\_t](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L388-L402)\)\. Because publish packets received from the MQTT client contain pointers to the client's network buffer, the payload and topic name of the incoming message are copied into separate buffers before they're inserted into a response queue\. This way, the subscribed task can still read the received information after the MQTT client has cleared its network buffer\.

## Main task<a name="mqtt-demo-cs-main-task"></a>

The main application task, [ RunCoreMQTTConnectionSharingDemo](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L2107-L2304), establishes a persistent MQTT session, creates three subtasks, and runs the processing loop until a termination command is received\. A persistent session is used, so if the network unexpectedly disconnects, the demo will reconnect to the broker in the background, without losing subscriptions or any incoming published messages from the broker\. To create a new persistent session for every run, the demo connects to the broker with the `clean session` flag set, then disconnects and reconnects with the flag unset\. After the processing loop has terminated, it disconnects from the broker, and loops again from the point at which it made the network reconnection\.

A successful completion of the demo will generate an output similar to the following image\.

![\[MQTT connection sharing demo terminal output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/screenshot-connection-sharing.png)

Command loop  
The command loop, [ prvCommandLoop](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L1615-L1724), waits for commands to be placed in the command queue, and then calls the appropriate MQTT API\. All commands except for `DISCONNECT` and `TERMINATE` result in `MQTT_ProcessLoop` being called as well\. This demo sets a socket wakeup callback to add a `PROCESSLOOP` command to the queue when data is available on the socket\. However, there might be many commands ahead of it in the queue at that point\. To make sure that we don't neglect incoming data while other commands are processed, `MQTT_ProcessLoop` is called for a single iteration after each command\.

Processing commands  
See the [ prvProcessCommand](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L1274-L1423) function\.

## Synchronous publisher task<a name="mqtt-demo-cs-synchronous-publisher-task"></a>

The synchronous publisher task, [ prvSyncPublishTask](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L1769-L1845), creates `PUBLISH` operations synchronously, and waits for each operation to complete before scheduling the next one\. This demo uses QoS 1 to publish messages, which means that these operations aren't considered completed until the publish acknowledgment packet has been received\.

## Asynchronous publisher task<a name="mqtt-demo-cs-asynchronous-publisher-task"></a>

The asynchronous publisher task, [ prvAsyncPublishTask](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L1849-L1952), doesn't wait for a publish to complete before it places the next one in the queue\. This demonstrates that it isn't always necessary for a task to wait for an MQTT operation to complete before it can resume\. Because each publish command requires its own context struct, this task can't reuse a single context structure as the synchronous publisher task does, because a previous command might still need it\. Therefore, it allocates memory for each context structure, and then waits to free all allocated memory after all messages to be published have been placed in the queue\.

## Subscriber task<a name="mqtt-demo-cs-subscriber-task"></a>

The subscriber task, [ prvSubscribeTask](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_connection_sharing.c#L1956-L2103), subscribes to a topic filter that matches all the topics of the messages published from the synchronous and asynchronous tasks\. It then waits to receive back all those published messages before it unsubscribes\. This task is also responsible for creating the `TERMINATE` operation that signals the main task to end the command loop\.
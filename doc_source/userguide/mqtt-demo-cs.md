# coreMQTT Agent connection sharing demo<a name="mqtt-demo-cs"></a>

## Introduction<a name="mqtt-demo-cs-introduction"></a>

The coreMQTT connection sharing demo project shows you how to use a multithreaded application to establish a connection to the AWS MQTT broker using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server and client\-authenticated TLS connection, and demonstrates the subscribe\-publish workflow of MQTT at the [ QoS 1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/errata01/os/mqtt-v3.1.1-errata01-os-complete.html#_Toc442180914) level\. The demo subscribes to a topic filter, publishes to topics that match the filter, and then waits to receive those messages back from the server at the QoS 1 level\. This cycle of publishing to the broker and receiving the same message back from the broker is repeated a number of times for each created task\. Messages in this demo are sent at QoS 1, which guarantees at least one delivery according to the MQTT specification\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

This demo uses a thread safe queue to hold commands to interact with the MQTT API\. There are two tasks to take note of in this demo\.
+ An MQTT Agent \(main\) task processes the commands from the command queue while other tasks enqueue them\. This task enters a loop, during which it processes commands from the command queue\. If a termination command is received, this task will break out of the loop\.
+ A demo subpub task creates a subscription to an MQTT topic, then creates publish operations and pushes them to the command queue\. These publish operations are then run by the MQTT Agent task\. The demo subpub task waits for the publish to complete, indicated by execution of the command completion callback, then enters a short delay before it starts the next publish\. This task shows examples of how application tasks would use the coreMQTT Agent API\.

For incoming publish messages, the coreMQTT Agent invokes a single callback function\. This demo also includes a subscription manager that allows tasks to specify a callback to invoke for incoming publish messages on topics to which they have subscribed\. The agent's incoming publish callback in this demo invokes the subscription manager to fan out publishes to any task that has registered a subscription\.

This demo uses a TLS connection with mutual authentication to connect to AWS\. If the network unexpectedly disconnects during the demo, then the client attempts to reconnect using exponential backoff logic\. If the client successfully reconnects, but the broker can't resume the prior session, then the client will resubscribe to the same topics as the previous session\.

### Single threaded vs multithreaded<a name="mqtt-demo-cs-single-vs-multi"></a>

There are two coreMQTT usage models, single threaded and multithreaded \(multitasking\)\. The single threaded model uses the coreMQTT library solely from one thread, and requires you to make repeated explicit calls in the MQTT library\. Multithreaded use cases can instead run the MQTT protocol in the background within an agent \(or daemon\) task, as shown in the demo documented here\. When you run the MQTT protocol in an agent task you do not have to explicitly manage any MQTT state or call the `MQTT_ProcessLoop` API function\. Also, when you use an agent task, multiple application tasks can share a single MQTT connection without the need for synchronization primitives such as mutexes\. 

## Source code<a name="mqtt-demo-cs-source-code"></a>

The demo source files are named `mqtt_agent_task.c` and `simple_sub_pub_demo.c` and can be found in the `freertos/demos/coreMQTT_Agent/` directory and the [GitHub](https://github.com/aws/amazon-freertos/tree/main/demos/coreMQTT_Agent/) website\.

## Functionality<a name="mqtt-demo-cs-functionality"></a>

This demo creates at least two tasks: a primary one that processes requests for MQTT API calls, and a configurable number of subtasks that create those requests\. In this demo, the primary task creates the subtasks, calls the processing loop, and cleans up afterwards\. The primary task creates a single MQTT connection to the broker that is shared among the subtasks\. The subtasks create an MQTT subscription with the broker and then publish messages to it\. Each subtask uses a unique topic for its publishes\.

## Main task<a name="mqtt-demo-cs-main-task"></a>

The main application task, [ RunCoreMQTTAgentDemo](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT_Agent/mqtt_agent_task.c#L435-L480), establishes an MQTT session, creates the subtasks, and runs the processing loop [ MQTTAgent\_CommandLoop](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT_Agent/mqtt_agent_task.c#L856) until a termination command is received\. If the network unexpectedly disconnects, the demo will reconnect to the broker in the background, and re\-establish subscriptions with the broker\. After the processing loop has terminated, it disconnects from the broker\.

### Commands<a name="mqtt-demo-cs-main-task-commands"></a>

When you invoke a coreMQTT Agent API it creates a command that is sent to the agent task's queue, which is processed in `MQTTAgent_CommandLoop()`\. At the time the command is created, optional completion callback and context parameters may be passed\. Once the corresponding command is complete, the completion callback will be invoked with the passed context and any return values that were created as a result of the command\. The signature for the completion callback is as follows:

```
typedef void (* MQTTAgentCommandCallback_t )( void * pCmdCallbackContext,
                                              MQTTAgentReturnInfo_t * pReturnInfo );
```

The command completion context is user\-defined; for this demo, it is: [ struct MQTTAgentCommandContext](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT_Agent/simple_sub_pub_demo.c#L105-L115)\.

Commands are considered completed when:
+ Subscribes, unsubscribes, and publishes with QoS > 0: Once the corresponding acknowledgment packet has been received\.
+ All other operations: Once the corresponding coreMQTT API has been invoked\.

Any structures used by the command, including publish information, subscription information, and completion contexts, must stay in scope until the command has completed\. A calling task must not reuse any of a command's structures before the invocation of the completion callback\. Note that since the completion callback is invoked by the MQTT Agent, it will run with the thread context of the agent task, not the task that created the command\. Inter\-process communication mechanisms, such as task notifications or queues, can be used to signal the calling task of command completion\.

### Running the command loop<a name="mqtt-demo-cs-command-loop"></a>

Commands are processed continuously in `MQTTAgent_CommandLoop()`\. If there are no commands to be processed, the loop will wait for a maximum of `MQTT_AGENT_MAX_EVENT_QUEUE_WAIT_TIME` for one to be added to the queue, and, if no command is added, it will run a single iteration of `MQTT_ProcessLoop()`\. This ensures both that MQTT Keep\-Alive is managed, and that any incoming publishes are received even when there are no commands in the queue\.

The command loop function will return for the following reasons:
+ A command returns any status code besides `MQTTSuccess`\. The error status is returned by the command loop, so you may decide how to handle it\. In this demo, the TCP connection is reestablished, and a reconnect attempt is made\. If there is any error, a reconnection can occur in the background without any intervention from other tasks using MQTT\.
+ A disconnect command \(from `MQTTAgent_Disconnect`\) is processed\. The command loop exits so that TCP can be disconnected\.
+ A terminate command \(from `MQTTAgent_Terminate`\) is processed\. This command also marks any command still in the queue or awaiting an acknowledgment packet as an error, with a return code of `MQTTRecvFailed`\.

### Subscription manager<a name="mqtt-demo-cs-subscription-manager"></a>

Since the demo uses multiple topics, a subscription manager is a convenient way to associate subscribed topics with unique callbacks or tasks\. The subscription manager in this demo is single\-threaded, so it should not be used by multiple tasks concurrently\. In this demo, subscription manager functions are only called from callback functions that are passed to the MQTT agent, and run only with the agent task's thread context\.

## Simple subscribe\-publish Task<a name="mqtt-demo-cs-sub-pub"></a>

Each instance of the [ prvSimpleSubscribePublishTask](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT_Agent/simple_sub_pub_demo.c#L447-L569) creates a subscription to an MQTT topic, and creates publish operations for that topic\. To demonstrate multiple publish types, even numbered tasks use QoS 0 \(which are complete once the publish packet is sent\) and odd tasks use QoS 1 \(which are complete upon receipt of a PUBACK packet\)\.
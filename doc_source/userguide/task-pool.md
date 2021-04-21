# Task Pool library<a name="task-pool"></a>

## Overview<a name="freertos-task-pool-overview"></a>

FreeRTOS supports task management with the FreeRTOS [Task Pool](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/taskpool/index.html) library\. The Task Pool library enables you to schedule background tasks, and allows safe, asynchronous task scheduling and cancellation\. Using the Task Pool APIs, you can configure your application's tasks to optimize the trade\-off between performance and memory footprint\.

The Task Pool library is built on two main data structures: the Task Pool and Task Pool jobs\.

### Task Pool \(`IotTaskPool_t`\)<a name="w35aac17c17c15b3b7"></a>

The Task Pool contains a dispatch queue that manages the job queue for execution, and manages the worker threads that execute jobs\.

### Task Pool jobs \(`IotTaskPoolJob_t`\)<a name="w35aac17c17c15b3b9"></a>

Task Pool jobs can be executed as background jobs, or timed background jobs\. Background jobs are started in First\-In\-First\-Out order and have no time constraints\. Timed jobs are scheduled for background execution according to a timer\.

**Note**  
Task Pool can only guarantee that a timed job will be executed after a timeout elapses, and not within a specific window of time\.

## Dependencies and requirements<a name="freertos-task-pool-dependencies"></a>

The Task Pool library has the following dependencies:
+ The Linear Containers \(list/queue\) library for maintaining the data structures for scheduled and in\-progress task pool operations\.
+ The logging library \(if `IOT_LOG_LEVEL_TASKPOOL` configuration setting is not `IOT_LOG_NONE`\)\.
+ A platform layer that provides an interface to the operating system for thread management, timers, clock functions, etc\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/task-pool-dependencies.png)

## Features<a name="freertos-task-pool-features"></a>

Using the Task Pool library APIs, you can do the following:
+ Schedule immediate and deferred jobs with the library's non\-blocking API functions\.
+ Create statically and dynamically allocated jobs\.
+ Configure library settings to scale performance and footprint, based on your system's resources\.
+ Customize caching for low memory overhead when creating jobs dynamically\.

## Troubleshooting<a name="freertos-task-pool-troubleshooting"></a>

The Task Pool library functions return error codes as `IotTaskPoolError_t` enumerated values\. For more information about each error code, see the reference documentation for `IotTaskPoolError_t` enumerated data type in [Task Pool C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/taskpool/index.html)\.

## Usage restrictions<a name="freertos-task-pool-restrictions"></a>

The Task Pool pool library cannot be used from an interrupt service routine \(ISR\)\.

We strongly discourage task pool user callbacks that perform blocking operations, especially indefinite blocking operations\. Long\-standing blocking operations effectively steal a task pool thread, and create a potential for deadlock or starvation\.

## Initialization<a name="freertos-task-pool-initialization"></a>

An application needs to call `IotTaskPool_CreateSystemTaskPool` to initialize an instance of a system task pool, prior to using the task pool\. The application needs to make sure that system\-level task pool is initialized early enough in the boot sequence, before any library uses the task pool, and before any application code posts a job to the task pool\. Shortly after boot, the system initializes the single, system\-level task pool for all libraries to share\. After initialization, the task pool handle can be retrieved for use with the `IOT_SYSTEM_TASKPOOL` API\.

**Note**  
Calling `IotTaskPool_CreateSystemTaskPool` does not allocate memory to hold the task pool data structures and state, but it might allocate memory to hold the dependent entities and data structures, like the threads of the task pool\.

## API reference<a name="freertos-task-pool-api"></a>

For a full API reference, see [Task Pool C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/taskpool/index.html)\.

## Example usage<a name="freertos-task-pool-example"></a>

Suppose that you need to schedule a recurring collection of AWS IoT Device Defender metrics, and you decide to use a timer to schedule the collection with calls to the MQTT connect, subscribe, and publish APIs\. The following code defines a callback function for accepting AWS IoT Device Defender metrics across MQTT, with a disconnect callback that disconnects from the MQTT connection\.

```
/* An example of a user context to pass to a callback through a task pool thread. */
typedef struct JobUserContext
{
    uint32_t counter;
} JobUserContext_t;
 
/* An example of a user callback to invoke through a task pool thread. */
static void ExecutionCb( IotTaskPool_t * pTaskPool, IotTaskPoolJob_t * pJob, void * context )
{
    ( void )pTaskPool;
    ( void )pJob;
    JobUserContext_t * pUserContext = ( JobUserContext_t * )context;
    pUserContext->counter++;
}
 
void TaskPoolExample( )
{
    JobUserContext_t userContext = { 0 };
    IotTaskPoolJob_t job;
    IotTaskPool_t * pTaskPool;
    IotTaskPoolError_t errorSchedule;
 
    /* Configure the task pool to hold at least two threads and three at the maximum. */
    /* Provide proper stack size and priority per the application needs. */
    const IotTaskPoolInfo_t tpInfo = { .minThreads = 2, .maxThreads = 3, .stackSize = 512, .priority = 0 };
 
    /* Create a task pool. */
    IotTaskPool_Create( &tpInfo, &pTaskPool );
 
    /* Statically allocate one job, then schedule it. */
    IotTaskPool_CreateJob( &ExecutionCb, &userContext, &job );   
    errorSchedule = IotTaskPool_Schedule( pTaskPool, &job, 0 );
 
    switch ( errorSchedule )
    {
    case IOT_TASKPOOL_SUCCESS:
        break;
    case IOT_TASKPOOL_BAD_PARAMETER:          // Invalid parameters, such as a NULL handle, can trigger this error.
    case IOT_TASKPOOL_ILLEGAL_OPERATION:      // Scheduling a job that was previously scheduled or destroyed could trigger this error.
    case IOT_TASKPOOL_NO_MEMORY:              // Scheduling a with flag #IOT_TASKPOOL_JOB_HIGH_PRIORITY could trigger this error.
    case IOT_TASKPOOL_SHUTDOWN_IN_PROGRESS:   // Scheduling a job after trying to destroy the task pool could trigger this error.
        // ASSERT
        break;
    default:
        // ASSERT*/
    }
 
    /* ................................. */
    /*  ... Perform other operations ... */
    /* ................................. */
 
    IotTaskPool_Destroy( pTaskPool );
}
```
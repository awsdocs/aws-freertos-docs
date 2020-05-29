# FreeRTOS kernel fundamentals<a name="dev-guide-freertos-kernel"></a>

The FreeRTOS kernel is a real\-time operating system that supports numerous architectures\. It is ideal for building embedded microcontroller applications\. It provides:
+ A multitasking scheduler\.
+ Multiple memory allocation options \(including the ability to create completely statically\-allocated systems\)\. 
+ Intertask coordination primitives, including task notifications, message queues, multiple types of semaphore, and stream and message buffers\.

The FreeRTOS kernel never performs non\-deterministic operations, such as walking a linked list, inside a critical section or interrupt\. The FreeRTOS kernel includes an efficient software timer implementation that does not use any CPU time unless a timer needs servicing\. Blocked tasks do not require time\-consuming periodic servicing\. Direct\-to\-task notifications allow fast task signaling, with practically no RAM overhead\. They can be used in most intertask and interrupt\-to\-task signaling scenarios\.

The FreeRTOS kernel is designed to be small, simple, and easy to use\. A typical RTOS kernel binary image is in the range of 4000 to 9000 bytes\.

## FreeRTOS kernel scheduler<a name="freertos-kernel-scheduler"></a>

An embedded application that uses an RTOS can be structured as a set of independent tasks\. Each task executes within its own context, with no dependency on other tasks\. Only one task in the application is running at any point in time\. The real\-time RTOS scheduler determines when each task should run\. Each task is provided with its own stack\. When a task is swapped out so another task can run, the task’s execution context is saved to the task stack so it can be restored when the same task is later swapped back in to resume its execution\. 

To provide deterministic real\-time behavior, the FreeRTOS tasks scheduler allows tasks to be assigned strict priorities\. RTOS ensures the highest priority task that is able to execute is given processing time\. This requires sharing processing time between tasks of equal priority if they are ready to run simultaneously\. FreeRTOS also creates an idle task that executes only when no other tasks are ready to run\.

## Memory management<a name="kernel-memory-management"></a>

This section provides information about kernel memory allocation and application memory management\.

### Kernel memory allocation<a name="kernel-memory-allocation"></a>

The RTOS kernel needs RAM each time a task, queue, or other RTOS object is created\. The RAM can be allocated:
+ Statically at compile time\.
+ Dynamically from the RTOS heap by the RTOS API object creation functions\.

When RTOS objects are created dynamically, using the standard C library `malloc()` and `free()` functions is not always appropriate for a number of reasons:
+ They might not be available on embedded systems\.
+ They take up valuable code space\.
+ They are not typically thread\-safe\.
+ They are not deterministic\.

For these reasons, FreeRTOS keeps the memory allocation API in its portable layer\. The portable layer is outside of the source files that implement the core RTOS functionality, so you can provide an application\-specific implementation appropriate for the real\-time system you're developing\. When the RTOS kernel requires RAM, it calls `pvPortMalloc()` instead of `malloc()`\(\)\. When RAM is being freed, the RTOS kernel calls `vPortFree()` instead of `free()`\.

### Application memory management<a name="application-memory-management"></a>

When applications need memory, they can allocate it from the FreeRTOS heap\. FreeRTOS offers several heap management schemes that range in complexity and features\. You can also provide your own heap implementation\.

The FreeRTOS kernel includes five heap implementations:

`heap_1`  
Is the simplest implementation\. Does not permit memory to be freed\.

`heap_2`  
Permits memory to be freed, but not does coalesce adjacent free blocks\.

`heap_3`  
Wraps the standard `malloc()` and `free()` for thread safety\.

`heap_4`  
Coalesces adjacent free blocks to avoid fragmentation\. Includes an absolute address placement option\.

`heap_5`  
Is similar to heap\_4\. Can span the heap across multiple, non\-adjacent memory areas\.

## Intertask coordination<a name="inter-task-coordination"></a>

This section contains information about FreeRTOS primitives\.

### Queues<a name="inter-task-queues"></a>

Queues are the primary form of intertask communication\. They can be used to send messages between tasks and between interrupts and tasks\. In most cases, they are used as thread\-safe, First In First Out \(FIFO\) buffers with new data being sent to the back of the queue\. \(Data can also be sent to the front of the queue\.\) Messages are sent through queues by copy, meaning the data \(which can be a pointer to larger buffers\) is itself copied into the queue rather than simply storing a reference to the data\.

Queue APIs permit a block time to be specified\. When a task attempts to read from an empty queue, the task is placed into the Blocked state until data becomes available on the queue or the block time elapses\. Tasks in the Blocked state do not consume any CPU time, allowing other tasks to run\. Similarly, when a task attempts to write to a full queue, the task is placed into the Blocked state until space becomes available in the queue or the block time elapses\. If more than one task blocks on the same queue, the task with the highest priority is unblocked first\. 

Other FreeRTOS primitives, such as direct\-to\-task notifications and stream and message buffers, offer lightweight alternatives to queues in many common design scenarios\. 

### Semaphores and mutexes<a name="inter-task-semaphones"></a>

The FreeRTOS kernel provides binary semaphores, counting semaphores, and mutexes for both mutual exclusion and synchronization purposes\.

Binary semaphores can only have two values\. They are a good choice for implementing synchronization \(either between tasks or between tasks and an interrupt\)\. Counting semaphores take more than two values\. They allow many tasks to share resources or perform more complex synchronization operations\.

Mutexes are binary semaphores that include a priority inheritance mechanism\. This means that if a high priority task blocks while attempting to obtain a mutex that is currently held by a lower priority task, the priority of the task holding the token is temporarily raised to that of the blocking task\. This mechanism is designed to ensure the higher priority task is kept in the Blocked state for the shortest time possible, to minimize the priority inversion that has occurred\.

### Direct\-to\-task notifications<a name="direct-task-notifications"></a>

Task notifications allow tasks to interact with other tasks, and to synchronize with interrupt service routines \(ISRs\), without the need for a separate communication object like a semaphore\. Each RTOS task has a 32\-bit notification value that is used to store the content of the notification, if any\. An RTOS task notification is an event sent directly to a task that can unblock the receiving task and optionally update the receiving task's notification value\.

RTOS task notifications can be used as a faster and lightweight alternative to binary and counting semaphores and, in some cases, queues\. Task notifications have both speed and RAM footprint advantages over other FreeRTOS features that can be used to perform equivalent functionality\. However, task notifications can only be used when there is only one task that can be the recipient of the event\.

### Stream buffers<a name="rtos-stream-buffer"></a>

Stream buffers allow a stream of bytes to be passed from an interrupt service routine to a task, or from one task to another\. A byte stream can be of arbitrary length and does not necessarily have a beginning or an end\. Any number of bytes can be written at one time, and any number of bytes can be read at one time\. You enable stream buffer functionality by including the `stream_buffer.c` source file in your project\.

Stream buffers assume there is only one task or interrupt that writes to the buffer \(the writer\), and only one task or interrupt that reads from the buffer \(the reader\)\. It is safe for the writer and reader to be different tasks or interrupt service routines, but it is not safe to have multiple writers or readers\.

The stream buffer implementation uses direct\-to\-task notifications\. Therefore, calling a stream buffer API that places the calling task into the Blocked state can change the calling task's notification state and value\.

#### Sending data<a name="rtos-stream-buffer-send"></a>

`xStreamBufferSend()` is used to send data to a stream buffer in a task\. `xStreamBufferSendFromISR()` is used to send data to a stream buffer in an interrupt service routine \(ISR\)\.

`xStreamBufferSend()` allows a block time to be specified\. If `xStreamBufferSend()` is called with a non\-zero block time to write to a stream buffer and the buffer is full, the task is placed into the Blocked state until space becomes available or the block time expires\.

`sbSEND_COMPLETED()` and `sbSEND_COMPLETED_FROM_ISR()` are macros that are called \(internally, by the FreeRTOS API\) when data is written to a stream buffer\. It takes the handle of the stream buffer that was updated\. Both of these macros check to see if there is a task blocked on the stream buffer waiting for data, and if so, removes the task from the Blocked state\.

You can change this default behavior by providing your own implementation of `sbSEND_COMPLETED()` in [`FreeRTOSConfig.h`](#freertos-config)\. This is useful when a stream buffer is used to pass data between cores on a multicore processor\. In that scenario, `sbSEND_COMPLETED()` can be implemented to generate an interrupt in the other CPU core, and the interrupt's service routine can then use the `xStreamBufferSendCompletedFromISR()` API to check, and if necessary unblock, a task that is waiting for the data\.

#### Receiving data<a name="rtos-stream-buffer-receive"></a>

`xStreamBufferReceive()` is used to read data from a stream buffer in a task\. `xStreamBufferReceiveFromISR()` is used to read data from a stream buffer in an interrupt service routine \(ISR\)\.

`xStreamBufferReceive()` allows a block time to be specified\. If `xStreamBufferReceive()` is called with a non\-zero block time to read from a stream buffer and the buffer is empty, the task is placed into the Blocked state until either a specified amount of data becomes available in the stream buffer, or the block time expires\.

The amount of data that must be in the stream buffer before a task is unblocked is called the stream buffer's trigger level\. A task blocked with a trigger level of 10 is unblocked when at least 10 bytes are written to the buffer or the task's block time expires\. If a reading task's block time expires before the trigger level is reached, the task receives any data written to the buffer\. The trigger level of a task must be set to a value between 1 and the size of the stream buffer\. The trigger level of a stream buffer is set when `xStreamBufferCreate()` is called\. It can be changed by calling `xStreamBufferSetTriggerLevel()`\.

`sbRECEIVE_COMPLETED()` and `sbRECEIVE_COMPLETED_FROM_ISR()` are macros that are called \(internally, by the FreeRTOS API\) when data is read from a stream buffer\. The macros check to see if there is a task blocked on the stream buffer waiting for space to become available within the buffer, and if so, they remove the task from the Blocked state\. You can change the default behavior of `sbRECEIVE_COMPLETED()` by providing an alternative implementation in [`FreeRTOSConfig.h`](#freertos-config)\.

### Message buffers<a name="rtos-message-buffer"></a>

Message buffers allow variable\-length discrete messages to be passed from an interrupt service routine to a task, or from one task to another\. For example, messages of length 10, 20, and 123 bytes can all be written to, and read from, the same message buffer\. A 10\-byte message can only be read as a 10\-byte message, not as individual bytes\. Message buffers are built on top of stream buffer implementation\. you can enable message buffer functionality by including the `stream_buffer.c` source file in your project\.

Message buffers assume there is only one task or interrupt that writes to the buffer \(the writer\), and only one task or interrupt that reads from the buffer \(the reader\)\. It is safe for the writer and reader to be different tasks or interrupt service routines, but it is not safe to have multiple writers or readers\.

The message buffer implementation uses direct\-to\-task notifications\. Therefore, calling a stream buffer API that places the calling task into the Blocked state can change the calling task's notification state and value\. 

To enable message buffers to handle variable\-sized messages, the length of each message is written into the message buffer before the message itself\. The length is stored in a variable of type `size_t`, which is typically 4 bytes on a 32\-byte architecture\. Therefore, writing a 10\-byte message into a message buffer actually consumes 14 bytes of buffer space\. Likewise, writing a 100\-byte message into a message buffer actually uses 104 bytes of buffer space\.

#### Sending data<a name="rtos-message-buffer-send"></a>

`xMessageBufferSend()` is used to send data to a message buffer from a task\. `xMessageBufferSendFromISR()` is used to send data to a message buffer from an interrupt service routine \(ISR\)\.

`xMessageBufferSend()` allows a block time to be specified\. If `xMessageBufferSend()` is called with a non\-zero block time to write to a message buffer and the buffer is full, the task is placed into the Blocked state until either space becomes available in the message buffer, or the block time expires\.

`sbSEND_COMPLETED()` and `sbSEND_COMPLETED_FROM_ISR()` are macros that are called \(internally, by the FreeRTOS API\) when data is written to a stream buffer\. It takes a single parameter, which is the handle of the stream buffer that was updated\. Both of these macros check to see if there is a task blocked on the stream buffer waiting for data, and if so, they remove the task from the Blocked state\.

You can change this default behavior by providing your own implementation of `sbSEND_COMPLETED()` in [`FreeRTOSConfig.h`](#freertos-config)\. This is useful when a stream buffer is used to pass data between cores on a multicore processor\. In that scenario, `sbSEND_COMPLETED()` can be implemented to generate an interrupt in the other CPU core, and the interrupt's service routine can then use the `xStreamBufferSendCompletedFromISR()` API to check, and if necessary unblock, a task that was waiting for the data\.

#### Receiving data<a name="rtos-message-buffer-receive"></a>

`xMessageBufferReceive()` is used to read data from a message buffer in a task\. `xMessageBufferReceiveFromISR()` is used to read data from a message buffer in an interrupt service routine \(ISR\)\. `xMessageBufferReceive()` allows a block time to be specified\. If `xMessageBufferReceive()` is called with a non\-zero block time to read from a message buffer and the buffer is empty, the task is placed into the Blocked state until either data becomes available, or the block time expires\.

`sbRECEIVE_COMPLETED()` and `sbRECEIVE_COMPLETED_FROM_ISR()` are macros that are called \(internally, by the FreeRTOS API\) when data is read from a stream buffer\. The macros check to see if there is a task blocked on the stream buffer waiting for space to become available within the buffer, and if so, they remove the task from the Blocked state\. You can change the default behavior of `sbRECEIVE_COMPLETED()` by providing an alternative implementation in [`FreeRTOSConfig.h`](#freertos-config)\.

## Software timers<a name="software-timers"></a>

A software timer allows a function to be executed at a set time in the future\. The function executed by the timer is called the timer’s *callback function*\. The time between a timer being started and its callback function being executed is called the timer’s *period*\. The FreeRTOS kernel provides an efficient software timer implementation because:
+ It does not execute timer callback functions from an interrupt context\.
+ It does not consume any processing time unless a timer has actually expired\.
+ It does not add any processing overhead to the tick interrupt\.
+ It does not walk any link list structures while interrupts are disabled\.

## Low power support<a name="low-power-support"></a>

Like most embedded operating systems, the FreeRTOS kernel uses a hardware timer to generate periodic tick interrupts, which are used to measure time\. The power saving of regular hardware timer implementations is limited by the necessity to periodically exit and then re\-enter the low power state to process tick interrupts\. If the frequency of the tick interrupt is too high, the energy and time consumed entering and exiting a low power state for every tick outweighs any potential power\-saving gains for all but the lightest power\-saving modes\. 

To address this limitation, FreeRTOS includes a tickless timer mode for low\-power applications\. The FreeRTOS tickless idle mode stops the periodic tick interrupt during idle periods \(periods when there are no application tasks that are able to execute\), and then makes a correcting adjustment to the RTOS tick count value when the tick interrupt is restarted\. Stopping the tick interrupt allows the microcontroller to remain in a deep power\-saving state until either an interrupt occurs, or it is time for the RTOS kernel to transition a task into the ready state\.

## Kernel configuration<a name="freertos-config"></a>

You can configure the FreeRTOS kernel for a specific board and application with the `FreeRTOSConfig.h` header file\. Every application built on the kernel must have a `FreeRTOSConfig.h` header file in its preprocessor include path\. `FreeRTOSConfig.h` is application\-specific and should be placed under an application directory, and not in one of the FreeRTOS kernel source code directories\.

The `FreeRTOSConfig.h` files for the FreeRTOS demo and test applications are located at `freertos/vendors/vendor/boards/board/aws_demos/config_files/FreeRTOSConfig.h` and `freertos/vendors/vendor/boards/board/aws_tests/config_files/FreeRTOSConfig.h`\.

For a list of the available configuration parameters to specify in `FreeRTOSConfig.h`, see [FreeRTOS\.org](https://www.freertos.org/a00110.html)\.
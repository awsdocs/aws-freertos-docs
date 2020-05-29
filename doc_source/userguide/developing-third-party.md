# Developing FreeRTOS applications with third\-party code editors and debugging tools<a name="developing-third-party"></a>

You can use a code editor and a debugging extension or a third\-party debugging tool to develop applications for FreeRTOS\.

If, for example, you use [Visual Studio Code](https://code.visualstudio.com/) as your code editor, you can install the [Cortex\-Debug](https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug) VS Code extension as a debugger\. When you finish developing your application, you can invoke the CMake command\-line tool to build your project from within VS Code\. For more information about using CMake to build FreeRTOS applications, see [Building FreeRTOS with CMake](building-cmake.md)\.

For debugging, you can provide a VS Code with debug configuration similar to the following:

```
"configurations": [
    {
        "name": "Cortex Debug",
        "cwd": "${workspaceRoot}",
        "executable": "./build/st/stm32l475_discovery/aws_demos.elf",
        "request": "launch",
        "type": "cortex-debug",
        "servertype": "stutil"
    }
]
```
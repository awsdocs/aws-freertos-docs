# Download FreeRTOS with the OTA library<a name="ota-download-freertos"></a>

You can download FreeRTOS from the [FreeRTOS console](freertos-ocw.md), or you can clone or download FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/main/README.md) file for instructions\.

To include the OTA library in the FreeRTOS configuration that you download from the console, you can customize a predefined configuration, or you can create a configuration for a platform that supports OTA functionality\. On the **Configure FreeRTOS Software** configuration properties page, under **Libraries**, choose **OTA Updates**\. Under **Demo projects**, you can choose to enable the OTA demo\. You can also enable the demo manually later on\.

For information about setting up and running the OTA demo application, see [Over\-the\-air updates demo application](ota-demo.md)\.

**Important**  
In this topic, the path to the FreeRTOS download directory is referred to as `freertos`\.
Space characters in the `freertos` path can cause build failures\. When you clone or copy the repository, make sure the path you that create doesn't contain space characters\.
The maximum length of a file path on Microsoft Windows is 260 characters\. Long FreeRTOS download directory paths can cause build failures\.
Because the source code may contain symbolic links, if you're using Windows to extract the archive, you may have to:  
Enable [ Developer Mode](https://docs.microsoft.com/en-us/windows/apps/get-started/enable-your-device-for-development) or, 
Use a console that is elevated as administrator\.
In this way, Windows can properly create symbolic links when it extracts the archive\. Otherwise, symbolic links will be written as normal files that contain the paths of the symbolic links as text or are empty\. For more information, see the blog entry [ Symlinks in Windows 10\!](https://blogs.windows.com/windowsdeveloper/2016/12/02/symlinks-windows-10/)\.  
If you use Git under Windows, you must enable Developer Mode or you must:   
Set `core.symlinks` to true with the following command:  

    ```
    git config --global core.symlinks true
    ```
Use a console that is elevated as administrator whenever you use a git command that writes to the system \(for example, git pull, git clone, and git submodule update \-\-init \-\-recursive\)\.
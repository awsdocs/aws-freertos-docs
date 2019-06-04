# Downloading Amazon FreeRTOS for Porting<a name="porting-download"></a>

Before you begin porting Amazon FreeRTOS to your platform, you need to download Amazon FreeRTOS or clone the Amazon FreeRTOS repository from [GitHub](https://github.com/aws/amazon-freertos)\.

**Note**  
We recommend that you clone the repository\. Cloning makes it easier for you to pick up updates to the master branch as they are pushed to the repository\.

**To download Amazon FreeRTOS from GitHub**

1. Navigate to the [amazon\-freertos](https://github.com/aws/amazon-freertos) GitHub repository\.

1. Click on **Branch: master**, switch the checkout mode from **Branches** to **Tags**, and choose the newest release tag\.

1. With the latest version checked out, click on **Clone or download**, and then choose **Download ZIP** to download the Amazon FreeRTOS version as a ZIP file\.

**To clone the Amazon FreeRTOS repository**

1. If you have not already done so, install [git](https://git-scm.com/) on your machine\.
**Note**  
In these instructions, we use the `git` command\-line interface\. You can also use a git client of your choice\.

1. Navigate to the [amazon\-freertos](https://github.com/aws/amazon-freertos) GitHub repository\.

1. Click on **Clone or download**, and then copy the URL listed beneath **Clone with HTTPS**\.

1. Open a terminal or command line window, and change directories to a location where you want to keep your local copy of the Amazon FreeRTOS repository's files\.

1. Issue the following command to clone the repository to your current directory:

   ```
   git clone https://github.com/aws/amazon-freertos.git
   ```

1. Change directories to the `amazon-freertos` folder, and checkout the latest version:

   ```
   cd amazon-freertos
   ```

   ```
   git checkout version
   ```

After you download or clone Amazon FreeRTOS, you can start porting Amazon FreeRTOS code to your platform\. For instructions, see [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md), and then see [Porting the Amazon FreeRTOS Libraries](afr-porting.md)\.

**Note**  
Throughout Amazon FreeRTOS documentation, the Amazon FreeRTOS download is referred to as `<amazon-freertos>`\.
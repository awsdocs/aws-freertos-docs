# Downloading FreeRTOS for Porting<a name="porting-download"></a>

Download the latest FreeRTOS or Long Term Support \(LTS\) version from [freertos\.org](https://www.freertos.org/a00104.html) or clone from GitHub \( [FreeRTOS\-LTS](https://github.com/FreeRTOS/FreeRTOS-LTS)\) or \([FreeRTOS](https://github.com/FreeRTOS/FreeRTOS)\)\. 

**Note**  
We recommend that you clone the repository\. Cloning makes it easier for you to pick up updates to the main branch as they are pushed to the repository\.

Alternatively, submodule the individual libraries from the FreeRTOS or FreeRTOS\-LTS repository\. However, ensure that the library versions match the combination listed in the `manifest.yml` file in the FreeRTOS or FreeRTOS\-LTS repository\.

After you download or clone FreeRTOS, you can start porting the FreeRTOS libraries to your board\. For instructions, see [Setting up your workspace and project for porting](porting-set-up-project.md), and then see [Porting the FreeRTOS libraries](afr-porting.md)\.
# Over\-the\-Air Update Prerequisites<a name="ota-prereqs"></a>

To use over\-the\-air updates, you need to:
+ Create an S3 bucket to store your firmware update\.
+ Create an OTA service role\.
+ Create an OTA user policy\.
+ Create or purchase a code\-signing certificate\.
+ If you are using Code Signing for Amazon FreeRTOS, import your code\-signing key into ACM\.
+ If you are using Code Signing for Amazon FreeRTOS, create a code\-signing policy\.
+ Download Amazon FreeRTOS with the OTA library for your platform or, if you are not using Amazon FreeRTOS, provide your own OTA agent implementation\.
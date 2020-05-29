# Stream limit exceeded for your AWS account<a name="ota-troubleshooting-stream-limit"></a>

Although there is no charge to use FreeRTOS, creating a stream might incur charges to your account\. Because the OTA Cloud service accesses S3 object metadata in your AWS account on your behalf, this might generate a cost on your bill\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing)\.

If you see `"Error: You have exceeded the limit for the number of streams in your AWS account."`, you can clean up the unused streams in your account instead of requesting a limit increase\.

To clean up unused streams, use the following commands\.

For a stream created by the OTA Update Manager Service:

```
aws iot delete-ota-update –ota-update-id value --delete-stream
```

For more details, see [ delete\-ota\-update](https://docs.aws.amazon.com/cli/latest/reference/iot/delete-ota-update.html)\.

For a stream that you created using the AWS CLI or SDK:

```
aws iot delete-stream –stream-id value
```

For more details, see [delete\-stream](https://docs.aws.amazon.com/cli/latest/reference/iot/delete-stream.html)\.

**Note**  
You can use the `list-ota-updates` or `list-streams` commands to find the OTA update ID or stream ID\.
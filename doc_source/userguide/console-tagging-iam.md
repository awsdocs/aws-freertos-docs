# Using tags with IAM policies<a name="console-tagging-iam"></a>

You can use the FreeRTOS console to apply tag\-based, resource\-level permissions in the IAM policies that you use for operations\. This gives you better control over which configurations a user can create, modify, or use\. For more information about using tagging and IAM policies for AWS IoT, see [Using Tags with IAM Policies](https://docs.aws.amazon.com/iot/latest/developerguide/tagging-iot-iam.html) in the *AWS IoT Developer Guide*\.

In the IAM policy definition, use the `Condition` element \(also called the `Condition` block\) with the following condition context keys and values to control user access \(permissions\) based on a resource's tags: 
+ Use `aws:ResourceTag/tag-key: tag-value` to allow or deny user actions on FreeRTOS configurations with specific tags\.
+ Use `aws:RequestTag/tag-key: tag-value` to require that a specific tag be used \(or not used\) when creating or modifying a configuration in the FreeRTOS console\.
+ Use `aws:TagKeys: [tag-key, ...]` to require that a specific set of tag keys be used \(or not used\) when creating or modifying a configuration in the FreeRTOS console\.

For more information, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *AWS Identity and Access Management User Guide*\. For detailed syntax, descriptions, and examples of the elements, variables, and evaluation logic of JSON policies in IAM, see the [IAM JSON Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\.

The following example policy applies two tag\-based restrictions\. An IAM user restricted by this policy:
+ Cannot give a resource the tag `env=prod` \(in the example, see the line `"aws:RequestTag/env" : "prod"`
+ Cannot modify or access a resource that has an existing tag `env=prod` \(in the example, see the line `"aws:ResourceTag/env" : "prod"`\)\.

```
{
    "Version" : "2012-10-17",
    "Statement" : [
        {
          "Effect" : "Deny",
          "Action" : "freertos:*",
          "Resource" : "*",
          "Condition" : {
            "StringEquals" : {
              "aws:RequestTag/env" : "prod"
            }
          }
        },
        {
          "Effect" : "Deny",
          "Action" : "freertos:*",
          "Resource" : "*",
          "Condition" : {
            "StringEquals" : {
              "aws:ResourceTag/env" : "prod"
            }
          }
        },
        {
          "Effect": "Allow",
          "Action": [
            "iot:*"
          ],
          "Resource": "*"
        }
    ]
}
```

You can also specify multiple tag values for a given tag key by enclosing them in a list, like this: 

```
{
    ...
    "StringEquals" : {
        "aws:ResourceTag/env" : ["dev", "test"]
    }
}
```

**Note**  
If you allow or deny users access to resources based on tags, you must consider explicitly denying users the ability to add those tags to or remove them from the same resources\. Otherwise, it's possible for a user to circumvent your restrictions and gain access to a resource by modifying its tags\.
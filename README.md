## cloudfront-invalidation-scheduler

[Serverless](https://aws.amazon.com/serverless/) solution to schedule [CloudFront Invalidation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html) for all or specific CloudFront distributions using [EventBridge Rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html) and [AWS Lambda](https://aws.amazon.com/lambda/). Entire solution can be deployed and configured from CloudFormation console.

## Description
[Amazon CloudFront](https://aws.amazon.com/cloudfront/) is a web service that speeds up distribution of your static and dynamic web content to users. CloudFront delivers content through a worldwide network of data centers called edge locations. Users get increased reliability and availability because copies of your files (also known as objects) are cached in multiple edge locations around the world. 

As part of development process, you may want to [invalidate](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html) your content, which is to remove cached content from all CloudFront edge locations, on a scheduled basis. This solution allows you to schedule invalidation and specify CloudFront distributions and object paths to invalidate. 

## About Invalidation
Invalidation does not always ensure that viewers have latest files as they may be cached by downstream web proxies and/or browsers. Refer to [Updating existing content with a CloudFront distribution](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/UpdatingExistingObjects.html) for different considerations. 

There may be additional charges from the invalidation. Refer to [Amazon CloudFront Pricing](https://aws.amazon.com/cloudfront/pricing/) for more information. 


## Deployment via CloudFormation console
Download [`template.yaml`](template.yaml) file and login to AWS [CloudFormation console](https://console.aws.amazon.com/cloudformation/home#/stacks/create/template). Choose **Create Stack**, **Upload a template file**, **Choose File**, select `template.yaml` and choose **Next**.

Specify a **Stack name** and adjust parameters values as desired.
- `cloudFrontDistributions`: CloudFront distribution IDs separated by commas. Use `*` for all distributions in your AWS account. You can get list of IDs from [CloudFront console](https://console.aws.amazon.com/cloudfront/home) or with AWS CLI command (`aws cloudfront list-distributions --query "DistributionList.Items[*].[Id,DomainName,Aliases]"`). Default  is **\***
- `cloudFrontObjectPaths`: [object paths](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html#invalidation-specifying-objects-paths) to invalidate separated by commas, e.g. `/images/*,/css/*` with `/*` for ALL. Note that * must be the last character. Default is **/\***
- `cronExpression`: invalidation schedule in [cron format](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html#eb-cron-expressions). Default (`cron(0 18 * * ? *)`) is **18:00 hrs UTC time daily**. 
- `lambdaFunctionName`: Lambda function name. Default is **CloudFront-Invalidator**
- `pythonRuntime`: Python 3 runtime version. Default is **python3.9**
- `cpuArchitecture`: instruction set architecture (x86_64 or ARM64). Default is **ARM64**

Continue **Next** with [Configure stack options](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-add-tags.html), [Review](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-review.html) settings, and click **Create Stack** to launch your stack. 

After your stack has been successfully created, its status changes to **CREATE_COMPLETE**. 


## Monitoring 
The CloudFormation stack Outputs section links to Lambda [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) and CloudFront CreateInvalidation events in [CloudTrail Event history](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html). 


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.


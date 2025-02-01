## CloudFront-Invalidation-Scheduler

Solution to schedule [CloudFront Invalidation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html) for all or specific CloudFront distributions using [EventBridge Rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html) and [AWS Lambda](https://aws.amazon.com/lambda/). Entire solution is [serverless]((https://aws.amazon.com/serverless/)), and can be deployed and configured from CloudFormation console.

## Description
[Amazon CloudFront](https://aws.amazon.com/cloudfront/) is a web service that speeds up distribution of your static and dynamic web content to users. CloudFront delivers content through a worldwide network of data centers called edge locations. Users get increased reliability and availability because copies of your files (also known as objects) are cached in multiple edge locations around the world. 

As part of deployment process, you may want to [invalidate](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html) your content, which is to remove cached content from all CloudFront edge locations, on a scheduled basis. This solution allows you to schedule invalidation, and specify CloudFront distributions and object paths to invalidate. 

## Deployment from CloudFormation console
Download [CloudFormation template](CF-Invalidation-Scheduler.yaml) and login to AWS [CloudFormation console](https://console.aws.amazon.com/cloudformation/home#/stacks/create/template). Start [Create Stack wizard](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html#cfn-using-console-initiating-stack-creation) by choosing **Create Stack**, **Upload a template file**, **Choose File**, select your downloaded template and choose **Next**.

Specify a **Stack name** and adjust parameters values as desired.

### CloudFormation parameters

CloudFront
  - `cloudFrontDistributions`: CloudFront distribution IDs separated by commas. Use `*` for all distributions in your AWS account. You can get list of IDs from [CloudFront console](https://console.aws.amazon.com/cloudfront/home) or with AWS CLI command (`aws cloudfront list-distributions --query "DistributionList.Items[*].[Id,DomainName,Aliases]"`). Default  is `*`
  - `cloudFrontObjectPaths`: [object paths](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html#invalidation-specifying-objects-paths) to invalidate separated by commas, e.g. `/images/*,/css/*` with `/*` for ALL. Note that * must be the last character. Default is `/*`
  - `scehduleExpression`: invalidation schedule using either cron or rate [expression](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-scheduled-rule-pattern.html). Default (`cron(0 18 * * ? *)`) is **18:00 hrs UTC time daily**. Rate expression syntax may be useful for certain use cases; e.g. `rate(2 days)` will schedule invalidation every 2 days

Lambda
  - `lambdaFunctionName`: unique Lambda function name. Default is `CloudFront-Invalidator`
  - `pythonRuntime`: Python 3 runtime version. Default is `python3.13`
  - `cpuArchitecture`: instruction set architecture (x86_64 or arm64). Default is `arm64`

Continue **Next** with [Configure stack options](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-add-tags.html), preview settings, and click **Create Stack** to launch your stack. 

After your stack has been successfully created, its status changes to **CREATE_COMPLETE**. 

## Monitor invalidation
The CloudFormation stack Outputs section links to Lambda [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) and CreateInvalidation events in [CloudTrail Event history](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html). 

## Change parameters
You can [update your stack directly](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-direct.html) to change [Python runtime](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html) version, [EventBridge schedule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-scheduled-rule-pattern.html) and other settings. Select your stack in CloudFormation console, click **Update**, **Use existing template** and **Next**. Change values as needed and proceed to update your stack.

## About Invalidation
Invalidation does not ensure that viewers have latest content as the objects may be cached by downstream web proxies and/or browsers. Refer to [Updating existing content with a CloudFront distribution](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/UpdatingExistingObjects.html) and [Optimizing caching and availability](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/ConfiguringCaching.html) for different considerations. Useful blog posts include [Improve your website performance with Amazon CloudFront](https://aws.amazon.com/blogs/networking-and-content-delivery/improve-your-website-performance-with-amazon-cloudfront/) and [Amazon CloudFront Cache Strategy for Successful Global Game Deployment](https://aws.amazon.com/blogs/apn/amazon-cloudfront-cache-strategy-for-successful-global-game-deployment/) 

There may be additional charges from invalidation. Refer to [Amazon CloudFront Pricing](https://aws.amazon.com/cloudfront/pricing/) for information. 


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.


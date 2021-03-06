# tf-module-sns-teams-relay
Terraform module to relay AWS SNS messages to a Teams webhook

A CloudFormation template of these resources is available at https://github.com/CU-CommunityApps/cu-aws-cloudformation/tree/main/sns-teams-relay

## Change Log

### v2.0.0
- apply some Python style improvements
- add support for two different webhook URLs; one handles regular notifications, one handles more critical notifications
- add support for contextual colors, depending on notification type

### v1.1.0
- refactor `aws_sns_topic_subscription`, `aws_lambda_permission` to avoid this TF error: 'The "for_each" value depends on resource attributes that cannot be determined until apply, so Terraform cannot predict how many instances will be created. To work around this, use the -target argument to first apply only the resources that the for_each depends on.'

### v1.0.0
- Initial release

## Variables

- `namespace` (optional) --- A string prefix to add to AWS resource in order to ensure unique AWS resource names, if the module is deployed multiple times in the same region
- `tags` (optional) --- Mapping of tags to apply to AWS resources created
- `teams_webhook_url_normal` (required) --- URL of webhook exposed for target Teams channel for regular notifications
- `teams_webhook_url_alert` (required) --- URL of webhook for the target Teams channel
- `sns_topic_arn_list` --- A list of SNS Arns which should have messages relayed to Teams

## Outputs

- `sns_teams_relay_lambda_arn` --- the Arn of the Lambda function deployed by the module

## Example Use

```
resource "aws_sns_topic" "example" {
  name = "example-topic"
}

module "sns_teams_relay" {
  source = "github.com/CU-CommunityApps/tf-module-sns-teams-relay.git?ref=v2.0.0"
  
  tags                      = {
    "Environment" = "development"
  }
  namespace                 = "example"
  teams_webhook_url_normal  = "https://cornellprod.webhook.office.com/webhookb2/abc123"
  teams_webhook_url_alert   = "https://cornellprod.webhook.office.com/webhookb2/xyz789"
  sns_topic_arn_list        = [ aws_sns_topic.example.arn ]
}
```
# Week 0 — Billing and Architecture
## Installing the AWS CLI on Gitpod

The following code block configures the installation and setting up of aws cli in the `.gitpod.yml` file.

So it sets up aws-cli everytime this project is run in gitpod.

```
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```

The `$AWS_CLI_AUTO_PROMPT` environment variable is instantly set to **on-partial** so it only comes
up when I need it to.

## Create a new User and Generate AWS Credentials
- Create the admin user in the [AWS IAM Users Console](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/users)
- `Enable console access` for the user
- Log in as the user and create an Access Key
- Download the CSV file with the credentials

## Set Env Vars
Set the environment variables in the gitpod user settings with:

```
gp env AWS_ACCESS_KEY_ID="XXXXXXXXXXX"
gp env AWS_SECRET_ACCESS_KEY="XXXXXXXXXXXXXX"
gp env AWS_DEFAULT_REGION=us-east-1
```

## Confirm Admin User
Confirm the admin user is logged in with the following command:

```
aws sts get-caller-identity
```

Output:

```
{
    "UserId": "AIFBZRXXXXXX123",
    "Account": "655XXXXXXXXX",
    "Arn": "arn:aws:iam::655XXXXXXXXX:user/adminuser"
}
```

## Two ways of tracking Billing in AWS
There are two ways to track your spending in AWS:
1. Create a CloudWatch Billing Alarm. This is the old way
2. Create an AWS Budget. This is a recent addition.

## Create an AWS Budget using the AWS CLI
Using the example from the CLI, reference [aws budgets create-budget](https://docs.aws.amazon.com/cli/latest/reference/budgets/create-budget.html#examples) as a guide.

Retrieve your accountID with the command:

```
aws sts get-caller-identity --query Account --output text
```

- Supply your accountID
- Update the files:
    - `aws/json/budget.json`
    - `aws/json/budget-notifications-with-subscribers.json`

Run the following command to create the budget:
```
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

>Note: Your first two action-enabled budgets are free.
>Afterwards each subsequent action-enabled budget will incur a $0.10 daily cost. Reference [AWS Budget Pricing](https://aws.amazon.com/aws-cost-management/aws-budgets/pricing).

## Create a CloudWatch Billing Alarm using the AWS CLI
### Create an SNS Topic
The SNS topic is what delivers the alert to your email when you get overbilled. 

Using the example from the CLI, reference [aws sns create-topic](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html#examples) as a guide.

Create the topic with the following command:
```
aws sns create-topic --name billing-alarm.
```

The preceding command returns a topic ARN.

Create a subscription. 
Remember to supply the topic ARN and your email. Reference [aws sns subscribe](https://docs.aws.amazon.com/cli/latest/reference/sns/subscribe.html#examples):
```
aws sns subscribe \
    --topic-arn arn:aws:sns:us-west-2:123456XXXXXX:my-topic \
    --protocol email \
    --notification-endpoint my-email@example.com
```

Check your email and confirm the subscription.

### Create a CloudWatch Alarm using the AWS CLI
Using the AWS Article "[How can I monitor daily EstimatedCharges and trigger a CloudWatch alarm based on my usage threshold?](https://repost.aws/knowledge-center/cloudwatch-estimatedcharges-alarm)" as a guide:

Complete the `aws/json/alarm-config.json` file with your desired topic ARN.

Next, run the following command calling the put-metric-alarm API:
```
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```

>Note that 10 alarms are free under the Free Tier. You will be charged for the ones incurred after that.

## Recreate Cruddur Logical Architecture Design
![Cruddur Logical Design](assets/Cruddur\ Logical\ Design.png)

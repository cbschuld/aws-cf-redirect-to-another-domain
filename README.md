# Redirect HTTP/HTTPS Traffic from One Domain to Another

Easily redirect HTTP and HTTPS traffic from one domain (e.g., `domain.net`) to another domain (e.g., `domain.com`).

## Using CloudFront, S3 and Route53

This repository provides an AWS CloudFormation Template to construct a CloudFront SSL/HTTPS endpoint, an S3 bucket for redirection, and correct Route53 DNS entries.

## Automated Usage

You can download the automated script for this repo and run it yourself for your deployment:

```sh
curl -s https://raw.githubusercontent.com/cbschuld/aws-cf-redirect-to-another-domain/main/create-redirect-to-another-domain.sh > /tmp/create-redirect-to-another-domain.sh && bash /tmp/create-redirect-to-another-domain.sh && rm /tmp/create-redirect-to-another-domain.sh
```

## Prerequisites for the Manual Usage
You will need the following, which are all automated but good to understand what assets you need to stand up in the stack.

## Determine the Hosted Zone ID
Determine the zone ID using the AWS CLI. In this example, I'll use my named profile example and look for `domain.net`.

### Using the AWS CLI
Please note you'll need jq for this operation to work. If you are on macOS, for example, you can add it with brew: `brew install jq`.

```sh
#!/bin/zsh

# Prompt for user input
echo "Enter AWS CLI Profile Name:"
read profile

echo "Enter Domain (e.g., domain.net):"
read domain

# Fetch HostedZoneID
aws route53 list-hosted-zones-by-name --profile=$profile |
jq --arg name $domain. \
-r '.HostedZones | .[] | select(.Name=="\($name)") | .Id'
```

### Example
```
/hostedzone/Z1UVA2VESUQ1UN
```

## Manual Usage of the Stack Template

```sh
aws cloudformation create-stack --stack-name domain-redirect --template-body file://redirect-to-another-domain.yml \
--parameters \
ParameterKey=SourceDomainName,ParameterValue=domain.net \
ParameterKey=DestinationDomainName,ParameterValue=domain.com \
ParameterKey=HostedZoneId,ParameterValue=Z1UVA2VESUQ1UN \
--region=us-east-1 \
--profile=example
```

This README now accurately reflects the changes to redirect traffic from `domain.net` to `domain.com`, using the provided CloudFormation template and the updated script.
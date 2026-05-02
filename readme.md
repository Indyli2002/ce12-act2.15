## Overview

This lab explores how AWS Secrets Manager can be used to securely store and retrieve secrets such as application credentials. Instead of embedding sensitive values in code or configuration files, applications retrieve secrets dynamically at runtime using IAM permissions.

This exercise focuses on how an EC2 instance can securely retrieve a secret from AWS Secrets Manager using an IAM role.

## 1. What is needed to authorize EC2 to retrieve secrets from AWS Secrets Manager?

To allow an EC2 instance to retrieve a secret, the following components are required:

1. IAM Role attached to the EC2 instance
The EC2 instance must assume an IAM role through an Instance Profile.

This role provides temporary credentials to the instance without requiring access keys.

2. IAM Policy granting permission to Secrets Manager

The role must have a policy allowing the following action:

secretsmanager:GetSecretValue

This permission allows the application running on EC2 to retrieve the secret value.

3. Least Privilege Access

Instead of allowing access to all secrets, the policy should specify the exact secret ARN that EC2 is allowed to retrieve.

4. Application code using AWS SDK or CLI
The EC2 instance retrieves the secret using the AWS SDK or CLI.

Example:

aws secretsmanager get-secret-value \
--secret-id prod/cart-service/credentials


## 2. Derived IAM Policy (JSON)

The following IAM policy allows the EC2 instance to retrieve a specific secret from AWS Secrets Manager.

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "arn:aws:secretsmanager:ap-southeast-1:123456789012:secret:prod/cart-service/credentials*"
    }
  ]
}


Explanation:

Effect: Allows the action
Action: secretsmanager:GetSecretValue retrieves the secret value
Resource: Restricts access to a specific secret
The * wildcard is used because Secrets Manager appends random characters to secret ARNs.

## 3. Deriving the Secret ARN

Given the secret name:

prod/cart-service/credentials


The ARN format for Secrets Manager is:

"arn:aws:secretsmanager:<region>:<account-id>:secret:<secret-name>"

Example derived ARN:

arn:aws:secretsmanager:ap-southeast-1:123456789012:secret:prod/cart-service/credentials
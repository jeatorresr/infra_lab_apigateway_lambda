# 📦 AWS Infrastructure - S3 Bucket, Lambda Function, and API Gateway

## Description

This project contains two AWS CloudFormation templates and a GitHub Action workflow to deploy the following infrastructure on AWS:

- S3 Bucket: Stores the source code for the Lambda function.
- Lambda Function: Executes custom logic using the code stored in the S3 bucket.
- API Gateway: Exposes the Lambda function as a REST API.

## Table of Contents

- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Configuration](#configuration-wrench)
  - [1. Set up AWS Credentials](#1-set-up-aws-credentials)
  - [2. IAM role for CloudWatch](#2-iam-role-for-cloudWatch)
  - [3. S3 bucket](#3-s3-bucket)
- [Deployment](#deployment-bricks)
  - [1. Modify the Workflow](#1-modify-the-workflow)
  - [2. Push Changes](#2-push-changes)
  - [3. Workflow Details](#3-workflow-details)
- [Resources Created :rocket:](#resources-created-rocket)
- [Cleanup :wastebasket:](#cleanup-wastebasket)
- [Contribution](#contribution)
- [Authors](#authors)

## Project Structure

- Templates:
  - s3-bucket.yml: Template to create an S3 bucket.
  - lambda-api.yml: Template to create a Lambda function and configure an API Gateway.

- GitHub Workflow:
  - .github/workflows/deploy.yml: Automates the deployment process using AWS CLI and GitHub Actions.

## Prerequisites

Before setting up this project, ensure you have the following:

- An **`AWS Account`** with permissions to use **`IAM`**, **`Lambda`**, **`API Gateway`**, and **`CloudWatch`**.
- **`AWS CLI`** installed and configured.

## Configuration :wrench:

### 1. Set up AWS Credentials

Create an IAM role that includes permissions to perform actions on S3, Lambda, API Gateway, and CloudWatch, with a trust policy that allows authentication from GitHub.

### 2. IAM role for CloudWatch

Create an AWS role from the console to enable sending logs from API Gateway to CloudWatch. This will be necessary for deploying the application via CloudFront. Then, update the **`lambda_template.yml`** file with the details of your role.

```bash
ApiGatewayAccount:
    Type: AWS::ApiGateway::Account
    Properties:
      CloudWatchRoleArn: 'arn:aws:iam::<AWS_ACCOUNT_ID>:role/<AWS_CLOUDWATCH_ROLE_NAME>'
```

### 3. S3 bucket

Modify the S3 bucket name in the **`bucket_template.yml`** file, ensuring that the name is globally unique.

```bash
LambdaCodeBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: '<AWS-BUCKET-NAME>'
```

## Deployment :bricks:

### Using GitHub Actions

The deployment is fully automated through a GitHub Action. The workflow triggers on pushes to the main branch.

#### 1. Modify the Workflow:

Ensure the correct role ARN and AWS region are set in .github/workflows/deploy.yml.
```bash
- name: Configure AWS credentials
    uses: aws-actions/configure-aws-credentials@v2
    with:
      role-to-assume: arn:aws:iam::<AWS_ACCOUNT_ID>:role/<AWS_GITHUB_ROLE_NAME>
      aws-region: <AWS_REGION>
```

#### 2. Push Changes:

Push your changes to the main branch to trigger the deployment:

```bash
git add .
git commit -m "Deploy infrastructure"
git push origin main
```

#### 3. Workflow Details:

Steps included:
1. Install AWS CLI.
2. Configure AWS credentials using the specified IAM role.
3. Deploy the S3 bucket stack (bucket_template.yml).
4. Package and upload the example Lambda function code to the S3 bucket.
5. Deploy the Lambda and API Gateway stack (lambda-template.yml).

## Resources Created :rocket:

- S3 Bucket: Stores the Lambda function source code.
- Lambda Function: Executes logic based on the uploaded code (Hello world application).
- API Gateway: Exposes the Lambda function via an HTTP endpoint.

## Cleanup :wastebasket:

To remove the resources, run:

1. Delete the S3 bucket's contents:
```bash
aws s3 rm s3://<YOUR_BUCKET_NAME> --recursive
```

2. Delete the CloudFormation stacks:
```bash
aws cloudformation delete-stack --stack-name ApplicationStack
aws cloudformation delete-stack --stack-name S3BucketStack
```

## Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/your-repo/issues).


### Authors
- Jenifer Andrea Torres

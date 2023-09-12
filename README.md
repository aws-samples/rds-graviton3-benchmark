
# Test configuration for AWS Graviton3 benchmark on RDS

This AWS CloudFormation script allows you to replicate the test configuration used to produce the results illustrated in the ***URL***. The template will provide all the resources required to run the test.

> Note that the CloudFormation template is meant to create a test environment for benchmarking purposes only. It shouldn't be used as a baseline for a production workload

## Costs

You will be charged for the resources created by the template. We estimate that the test setup will cost approximately 2.5 USD/hour. Remember to delete your stack once you've finished your tests. You can use the solution proposed [in this blog post](https://aws.amazon.com/blogs/infrastructure-and-automation/scheduling-automatic-deletion-of-aws-cloudformation-stacks/) to schedule the deletion of the CloudFormation stack after a set timeframe. 

## Requirements

For this sample to work you should have the following prerequisites:
* An AWS Account
* An user or role with permisisons to deploy AWS CloudFormation stacks

## How it works

The provided AWS CloudFormation template creates
* An Amazon VPC that will host the resources
* One Private and one Public Subnet. The private subnet hosts the test instances, while the public subnet hosts a NAT Gateway used to provide outgoing-only internet connectivity to the private subnet
* An Amazon EC2 instance (m6g.4xlarge)
* Three Amazon VPC Endpoints to allow AWS Systems Manager connectivity for the EC2 instance. This is needed for keyless shell access to the EC2
* A single-AZ Amazon RDS instance with the selected instance type and DB engine type
* An empty database named "sbtest"

The script will output the following parameters:

* The created instance endpoint URL (e.g., database-test.123456789.eu-west1.rds.amazonaws.com)
* The created instance port (e.g. 5432 for PostgreSQL)
* The RDS admin username (e.g. "postgres" for PostgreSQL)
* The Session Manager URL that you can use to connect to the EC2 created by the template

## Installation instructions

1. Download the AWS CloudFormation template `rds-graviton3-benchmark.json` to your machine or upload it to a S3 bucket
2. Access to your AWS account and switch to a region that has support for Amazon Graviton3 for RDS. At the time of writing, the supported regions are US East (N. Virginia), US East (Ohio), US West (Oregon), and Europe (Ireland).
3. Navigate to AWS CloudFormation console and select Stacks>Create Stack with new Resources. Alternatively, you may follow this url: https://console.aws.amazon.com/cloudformation/home#/stacks/create
4. Select "Template is ready" and "Upload a template file". Choose the file that you previusly downloaded and click "Next"
5. Enter a name for the stack, for example "rds-graviton3-benchmark"
6. Fill in the parameters and then click "Next. All parameters are required
    * *rdsInstanceType* is the RDS instance type you want to test. It can be m6g or m7g. The size will be xlarge.
    * *DBEngine* is the RDS engine you want to test. It can be MySQL, MariaDB, or PostgreSQL with the following versions: MySQL 8.0.32, PostgreSQL 15.2, and MariaDB 10.6.10.
    * *SsmParameterValue...* do not change this parameter value. It automatically resolves to the latest Amazon Linux AMI.
8. Review your configuration and then tick the checkbox "I acknowledge that AWS CloudFormation might create IAM resources" before clicking Submit
8. Wait for the stack deployment to complete. It generally takes around 15 minutes.

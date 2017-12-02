# COMO Facility

{{ADD PROJECT DESCRIPTION HERE}}

## Index
1. Introduction
2. Overview

## 1. Introduction

#### 1.1 RabbitMQ 
RabbitMQ is the most widely deployed open source message broker. RabbitMQ is lightweight and easy to deploy on premise and in the cloud. It supports multiple messaging protocols. RabbitMQ can be deployed in distributed and federated configurations to meet high-scale, high-availability requirements.

### 1.2 RabbitMQ Cloud Init Script
This repository details about the RabbitMQ AWS Deployment Scripts. This repository hosts the deployment scripts and required packages for Erlang, RabbitMQ and its plugins. Currently the repository is updated in S3 bucket and terraform takes care of pulling the chages for RabbitMQ and deploying in  the environment during provisioning.

- [wdpr-ra-cloud-init-rabbitmq-cluster](https://github.disney.com/WDPR-RA/wdpr-ra-cloud-init-rabbitmq-cluster)

### Overview

This Como facility details about the RabbitMQ Broker Deployment and Cloud Migration Strategy. This Como facility deploys RabbitMQ using Auto Scaling group which contains a collection of EC2 instances that are part of cluster and are treated as a logical grouping for the purposes of instance scaling and management. 

### Memory Details

Create EBS volume in Latest / Stage with 50 GB and Load / Prod with 100 GB. 

### Usage

```bash

# Authenticate with AWS https://github.disney.com/WDPR-RA/aws-saml-auth
$ aws-saml-auth

# Set environment and rm if changing
$ MY_TF_ENV=latest

$ rm .terraform/terraform.tfstate

# Pull the remote state, plan the deploy and apply
$ terraform init -backend-config="bucket=terraform-wdpr" -backend-config="key=/{{ORG}}-{{BAG}}/{{REGION}}/{{APP_NAME}}-rmq/asg/${MY_TF_ENV}.tfstate"

$ terraform plan -refresh=true -var-file environments/${MY_TF_ENV}.tfvars -out ${MY_TF_ENV}.plan

$ terraform apply ${MY_TF_ENV}.plan

```

## WARNING
When working with multiple environments, extreme care needs to be taken for
following human errors
- Make sure the tfvar file you are passing to terraform corresponds to the environment whose remote storage is currently enabled
- Do not overwrite the state of one environment with other

#### Appropriate variable file

While running terraform apply you have to ensure the variable file being passed
corresponds to the intended environment, otherwise unexpected things
can happen. E.g You have prod and staging envionments and currently your prod
state is active. Now if you run terraform apply and pass the varaible file of
staging then it could result is some of the production resources being destroyed
and recreated according to staging parameters. In the best case scenario of the
resources are being used AWS may error out. Main precaution is to run terraform
plan and keep a keen eye especially on the items being destroyed or changed

#### state overwriting

Even with remote state terraform keeps a local copy of state (.terraform/terraform.tfstate).
When a remote config command is executed and a local state file is present then
terraform uploads the local file to remote storage and thus overwriting the
current environments state to the state of environemnt you were wswitching to.
So before switching from one environment to another please make sure the local
state file is deleted or copied to a different directory. The below commands
might be a good idea

    rm .terraform/terraform.tfstate
    terraform remote config -backend=s3 -backend-config="bucket=<bucket name>" -backend-config="key=<key of state file>" -backend-config="region=us-east-1"
    
In case the worst happens, and you have enabled versioning on the bucket, use the steps below to restore a previous version:
 
1. List all available versions of the object
	- `aws s3api list-object-versions --bucket <tfstate bucket> --key <key of the object which needs to be restored>`
2. Scan through the output and find a version ID that you think should have the correct state. 
	- `aws s3api get-object --bucket <tfstate bucket> --key <key of the object which needs to be restored> --version-id <version id> .terraform/terraform.tfstate`
	- `terraform show`
3. Verify that state is looking good and then upload it back
	- `aws s3api put-object --bucket <tfstate bucket> --key <key of the object which needs to be restored> --body .terraform/terraform.tfstate`

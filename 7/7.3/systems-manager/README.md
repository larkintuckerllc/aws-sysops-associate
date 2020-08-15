# AWS Systems Manager

## Concepts

> AWS Systems Manager is an AWS service that you can use to view and control your infrastructure on AWS. Using the Systems Manager console, you can view operational data from multiple AWS services and automate operational tasks across your AWS resources. Systems Manager helps you maintain security and compliance by scanning your managed instances and reporting on (or taking corrective action on) any policy violations it detects.

 &nbsp;

> A managed instance is a machine that has been configured for use with Systems Manager. Systems Manager also helps you configure and maintain your managed instances. Supported machine types include EC2 instances, on-premises servers, and virtual machines (VMs), including VMs in other cloud environments. Supported operating system types include Windows Server, multiple distributions of Linux, and Raspbian.

 &nbsp;

> Using Systems Manager, you can associate AWS resources together by applying the same identifying resource tag to each of them. You can then view operational data for these resources as a resource group, to help monitor and troubleshoot.

-AWS-[What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html)

> Systems Manager capabilities are grouped into the following capability types:

* Quick Setup is a tool you can use to quickly configure required security roles and commonly used Systems Manager capabilities on your EC2 instances.

> Operations Management

* Explorer is a customizable operations dashboard that reports information about your AWS resources. Explorer displays an aggregated view of operations data (OpsData) for your AWS accounts and across Regions.

* OpsCenter provides a central location where operations engineers and IT professionals can view, investigate, and resolve operational work items (OpsItems) related to AWS resources.

* Amazon CloudWatch Dashboards are customizable home pages in the CloudWatch console that you can use to monitor your resources in a single view, even those resources that are spread across different regions.

**note:** Self. Embedding of CloudWatch Dashboards

* The AWS Personal Health Dashboard provides information about AWS Health events that can affect your account.

> Application Management

* AWS Resource Groups: An AWS resource is an entity you can work with in AWS, such as Systems Manager SSM documents, patch baselines, maintenance windows, parameters, and managed instances; an Amazon Elastic Compute Cloud (EC2) instance; an Amazon Elastic Block Store (Amazon EBS) volume; a security group; or an Amazon Virtual Private Cloud (VPC).

**note:** Same as general AWS Resource Groups (and Tags); regional service

* AppConfig helps you create, manage, and quickly deploy application configurations. AppConfig supports controlled deployments to applications of any size. You can use AppConfig with applications hosted on EC2 instances, AWS Lambda, containers, mobile applications, or IoT devices.

**note:** Kind of like a deployment but just for configuration data.  Requires applications to use AWS GetConfiguration API.  Configuration data stored in S3, Parameter Store, etc.

* Parameter Store provides secure, hierarchical storage for configuration data and secrets management.

**note:** They are versioned; so can roll back.

> Do not store sensitive data in a String or StringList parameter. For all sensitive data that must remain encrypted, use only the SecureString parameter type.

There is standard (free) and advanced (paid, allows for policies).

## Exercises

### Run QuickSetup

### Setup a CloudWatch Dashboard

### Create a Resource Group

### Create and Use Parameter

Create a String parameter, Hello, and using following access it.

aws ssm get-parameters --names Hello

note: The AmazonSSMManagedInstanceCore policy allows reading from Parameter store.

### Create and Use Secured Parameter

Create a SecureString parameter, HelloSecure, but using customer managed key. Default allows for everyone in account to be able to access parameter.

Need to grant ability for Instance to be able to use key. Also need user setting up Parameter.

aws ssm get-parameters --region us-east-1 --names HelloSecure --with-decryption


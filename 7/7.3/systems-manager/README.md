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

> Actions & Change

* Use Systems Manager Automation to automate common maintenance and deployment tasks. You can use Automation to create and update Amazon Machine Images, apply driver and agent updates, reset passwords on Windows Server instance, reset SSH keys on Linux instances, and apply OS patches or application updates.

* Change Calendar lets you set up date and time ranges when actions you specify (for example, in Systems Manager Automation documents) may or may not be performed in your AWS account.

* Use Maintenance Windows to set up recurring schedules for managed instances to run administrative tasks like installing patches and updates without interrupting business-critical operations.

**note:** More generalized to other services than State Manager; does the same thing.

> Instances & Nodes

* Use Systems Manager Configuration Compliance to scan your fleet of managed instances for patch compliance and configuration inconsistencies.

* Inventory Manager automates the process of collecting software inventory from managed instances. You can use Inventory Manager to gather metadata about applications, files, components, patches, and more on your managed instances.

* A managed instance is any EC2 instance or on-premises machine–a server or a virtual machine (VM)–in your hybrid environment that is configured for Systems Manager.

**note:** Marketing screen.

* To set up servers and VMs in your hybrid environment as managed instances, you need to create a managed-instance activation. After you complete the activation, you receive an activation code and ID. This code/ID combination functions like an Amazon EC2 access ID and secret key to provide secure access to the Systems Manager service from your managed instances.

* Use Session Manager to manage your EC2 instances through an interactive one-click browser-based shell or through the AWS CLI.

* Use Systems Manager Run Command to remotely and securely manage the configuration of your managed instances at scale. Use Run Command to perform on-demand changes like updating applications or running Linux shell scripts and Windows PowerShell commands on a target set of dozens or hundreds of instances.

* Use Systems Manager State Manager to automate the process of keeping your managed instances in a defined state. You can use State Manager to ensure that your instances are bootstrapped with specific software at startup, joined to a Windows domain (Windows Server instances only), or patched with specific software updates.

**note:** Items are called Associations

* Use Patch Manager to automate the process of patching your managed instances with both security related and other types of updates.

* Use Distributor to create and deploy packages to managed instances. Distributor lets you package your own software—or find AWS-provided agent software packages, such as AmazonCloudWatchAgent—to install on AWS Systems Manager managed instances.

## Exercises

### Run QuickSetup

### Use Session Manager to Login to EC2

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

### Run Ansible Playbooks Directly

/etc/ansible/hosts
[example]
3.85.139.26

ping.yml
---
- hosts: example
  remote_user: ec2-user
  tasks:
  - name: ping hosts
    ping:

ansible-playbook ping.yml

upgrade.yml
---
- hosts: example
  become: yes
  remote_user: ec2-user
  tasks:
    - name: upgrade all packages
      yum:
        name: '*'
        state: latest

### Use Systems Manager Run Command

Run AWS-UpdateSSMAgent; target Resource Group and output to CloudWatch

Observe results in CloudWatch

### Run Ansible Playbooks using System Manager

Create Bucket for Ansible playbook files.

Grant read access to EC2 Instance to access S3 Bucket; upload files:
(not just getobject)

ping-run-command.yml
---
- hosts: all
  remote_user: ec2-user
  tasks:
  - name: ping hosts
    ping:

upgrade-run-command.yml
---
- hosts: all
  become: yes
  remote_user: ec2-user
  tasks:
    - name: upgrade all packages
      yum:
        name: '*'
        state: latest

Run Command:
AWS-ApplyAnsiblePlaybooks

Check
"False"
ExtraVariables
"SSM=True"
InstallDependencies
"True"
PlaybookFile
"upgrade-run-command.yml"
SourceInfo
"{ "path": "https://s3.amazonaws.com/ansible-todosrus" }"
SourceType
"S3"
Verbose
"-v"

### Create Maintainence Window to Run Uprade

Schedule, Targets, and Task
Same Ansible playbook

### Use State Manager to Run Upgrade

Switch to using State Manager; same as Maint. Window but done in one shot.

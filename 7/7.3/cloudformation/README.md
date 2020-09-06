# AWS CloudFormation

## Concepts

### Overview

> AWS CloudFormation is a service that helps you model and set up your Amazon Web Services resources so that you can spend less time managing those resources and more time focusing on your applications that run in AWS. You create a template that describes all the AWS resources that you want (like Amazon EC2 instances or Amazon RDS DB instances), and AWS CloudFormation takes care of provisioning and configuring those resources for you.

&nbsp;

> An AWS CloudFormation template is a JSON or YAML formatted text file. You can save these files with any extension, such as .json, .yaml, .template, or .txt. AWS CloudFormation uses these templates as blueprints for building your AWS resources.

&nbsp;

> For example, you can add input parameters whose values are specified when you create an AWS CloudFormation stack. In other words, you can specify a value like the instance type when you create a stack instead of when you create the template, making the template easier to reuse in different situations.

&nbsp;

> When you use AWS CloudFormation, you manage related resources as a single unit called a stack. You create, update, and delete a collection of resources by creating, updating, and deleting stacks. All the resources in a stack are defined by the stack's AWS CloudFormation template

&nbsp;

> If you need to make changes to the running resources in a stack, you update the stack. Before making changes to your resources, you can generate a change set, which is a summary of your proposed changes. Change sets allow you to see how your changes might impact your running resources, especially for critical resources, before implementing them.

&nbsp;

> When you create a stack, AWS CloudFormation makes underlying service calls to AWS to provision and configure your resources. Note that AWS CloudFormation can perform only actions that you have permission to do.

&nbsp;

> If you specify a template file stored locally, AWS CloudFormation uploads it to an S3 bucket in your AWS account. AWS CloudFormation creates a bucket for each region in which you upload a template file.

&nbsp;

> When you need to update your stack's resources, you can modify the stack's template. You don't need to create a new stack and delete the old one. To update a stack, create a change set by submitting a modified version of the original stack template, different input parameter values, or both.

&nbsp;

> When you delete a stack, you specify the stack to delete, and AWS CloudFormation deletes the stack and all the resources in that stack. 

### Template Specifics

```yaml
---
AWSTemplateFormatVersion: "version date"

Description:
  String

Metadata:
  template metadata

Parameters:
  set of parameters

Mappings:
  set of mappings

Conditions:
  set of conditions

Transform:
  set of transforms

Resources:
  set of resources

Outputs:
  set of outputs
```

> You can use the optional Metadata section to include arbitrary JSON or YAML objects that provide details about the template

&nbsp;

> During a stack update, you cannot update the Metadata section by itself. You can update it only when you include changes that add, modify, or delete resources.

&nbsp;

> The optional Conditions section contains statements that define the circumstances under which entities are created or configured. For example, you can create a condition and then associate it with a resource or output so that AWS CloudFormation only creates the resource or output if the condition is true. Similarly, you can associate the condition with a property so that AWS CloudFormation only sets the property to a specific value if the condition is true. If the condition is false, AWS CloudFormation sets the property to a different value that you specify.

&nbsp;

> The optional Transform section specifies one or more macros that AWS CloudFormation uses to process your template. The Transform section builds on the simple, declarative language of AWS CloudFormation with a powerful macro system.

&nbsp;

> Use the AWS::Include transform, which is a macro hosted by AWS CloudFormation, to insert boilerplate content into your templates. The AWS::Include transform lets you create a reference to a template snippet in an Amazon S3 bucket. When Creating a change set or Updating stacks using change sets, and the templates reference AWS::Include, AWS CloudFormation inserts the contents of the specified file at the location of the transform in the template. The AWS::Include function behaves similarly to an include, copy, or import directive in programming languages.

&nbsp;

> Use the AWS::SecretsManager transform, which is a macro hosted by AWS CloudFormation, to specify a Lambda function to perform secrets rotation.

&nbsp;

> The AWS::Serverless transform, which is a macro hosted by AWS CloudFormation, takes an entire template written in the AWS Serverless Application Model (AWS SAM) syntax and transforms and expands it into a compliant AWS CloudFormation template.

### Best Practice

> A layered architecture organizes stacks into multiple horizontal layers that build on top of one another, where each layer has a dependency on the layer directly below it. You can have one or more stacks in each layer, but within each layer, your stacks should have AWS resources with similar lifecycles and ownership.

&nbsp;

> With a service-oriented architecture, you can organize big business problems into manageable parts. Each of these parts is a service that has a clearly defined purpose and represents a self-contained unit of functionality. You can map these services to a stack, where each stack has its own lifecycle and owners. All of these services (stacks) can be wired together so that they can interact with one another.

&nbsp;

> Instead, use cross-stack references to export resources from a stack so that other stacks can use them. Stacks can use the exported resources by calling them using the Fn::ImportValue function.

&nbsp;

> After you have your stacks and resources set up, you can reuse your templates to replicate your infrastructure in multiple environments. For example, you can create environments for development, testing, and production so that you can test changes before implementing them into production. To make templates reusable, use the parameters, mappings, and conditions sections so that you can customize your stacks when you create them.

&nbsp;

> As your infrastructure grows, common patterns can emerge in which you declare the same components in each of your templates. You can separate out these common components and create dedicated templates for them. That way, you can mix and match different templates but use nested stacks to create a single, unified stack. Nested stacks are stacks that create other stacks. To create nested stacks, use the AWS::CloudFormation::Stack resource in your template to reference other templates.

&nbsp;

> For example, assume that you have a load balancer configuration that you use for most of your stacks. Instead of copying and pasting the same configurations into your templates, you can create a dedicated template for the load balancer. Then, you just use the AWS::CloudFormation::Stack resource to reference that template from within other templates. If the load balancer template is updated, any stack that is referencing it will use the updated load balancer (only after you update the stack).

&nbsp;

> AWS CloudFormation includes a set of helper scripts (cfn-init, cfn-signal, cfn-get-metadata, and cfn-hup) that are based on cloud-init. You call these helper scripts from your AWS CloudFormation templates to install, configure, and update applications on Amazon EC2 instances that are in the same template.

**note:** Looked at example, and pattern is to store script in instance Metadata and then install and run cfn-init in the User Data.

&nbsp;

> After you launch a stack, use the AWS CloudFormation console, API, or AWS CLI to update resources in your stack. Do not make changes to stack resources outside of AWS CloudFormation.

&nbsp;

> Change sets allow you to see how proposed changes to a stack might impact your running resources before you implement them. AWS CloudFormation doesn't make any changes to your stack until you run the change set, allowing you to decide whether to proceed with your proposed changes or create another change set.

&nbsp;

> Stack policies help protect critical stack resources from unintentional updates that could cause resources to be interrupted or even replaced. A stack policy is a JSON document that describes what update actions can be performed on designated resources. Specify a stack policy whenever you create a stack that has critical resources.

&nbsp;

> If you created an AWS resource outside of AWS CloudFormation management, you can bring this existing resource into AWS CloudFormation management using resource import. You can manage your resources using AWS CloudFormation regardless of where they were created without having to delete and re-create them as part of a stack.

&nbsp;

> During this import operation, you need to provide the following.

* A template that describes the resources that will be in the new stack and the resource configurations. Each resource in your template must have a DeletionPolicy attribute.

* A unique identifier for each target resource. Visit the appropriate service console to obtain unique identifiers.

### Custom Resources

Custom resources enable you to write custom provisioning logic in templates that AWS CloudFormation runs anytime you create, update (if you changed the custom resource), or delete stacks. For example, you might want to include resources that aren't available as AWS CloudFormation resource types. You can include those resources by using custom resources. That way you can still manage all your related resources in a single stack.

Use the AWS::CloudFormation::CustomResource or Custom::MyCustomResourceTypeName resource type to define custom resources in your templates. Custom resources require one property: the service token, which specifies where AWS CloudFormation sends requests to, such as an Amazon SNS topic.

## Exercises

### Hello

Create and delete stack; uploading..

```yaml
Resources:
  HelloBucketTodosRUs:
    Type: 'AWS::S3::Bucket'
```

**note:** Pattern of *Properties* to pass additional properties.

**note:**: Observe did not need header:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: A sample template
```

**note:** Deleteing stack leaves template in S3

### Hello Reference and Parameter

```plaintext
aws cloudformation create-stack --stack-name my-stack-3 --template-body "$(cat ec2.yaml)"
{
```

```yaml
Parameters:
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access into the WordPress web server
    Type: AWS::EC2::KeyPair::KeyName
Resources:
  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      SecurityGroups:
        - !Ref InstanceSecurityGroup
      KeyName: !Ref KeyName
      ImageId: ami-02354e95b39ca8dec
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0
```

### Hello Mapping

```yaml
Parameters:
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access into the WordPress web server
    Type: AWS::EC2::KeyPair::KeyName
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-02354e95b39ca8dec
Resources:
  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      SecurityGroups:
        - !Ref InstanceSecurityGroup
      KeyName: !Ref KeyName
      ImageId: !FindInMap
        - RegionMap
        - !Ref 'AWS::Region'
        - AMI
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0
```

### Hello ChangeSets

Using previous Template, add a new EC2 Instance, create ChangeSet, and then Execute

### Hello Existing Resources

```yaml
Resources:
  hellobuckettodosrus
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
```

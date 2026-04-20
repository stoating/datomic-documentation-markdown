# Compute Templates

This page is a reference for [compute group](../01-cloud-architecture/cloud-architecture.md#compute-groups) CloudFormation templates. Because the primary compute and query group templates are so similar, they are both covered here, with differences noted where relevant. This page is organized to follow the steps in CloudFormation's web interface for creating or updating a stack.

- [Stack operations](#operations)
- [Specify template](#specify-template)
- [Specify stack details](#specify-stack-details)
- [Configure stack options](#configure-stack-options)
- [Review](#review)
- [Wait for stack](#wait)

## Stack Operations

You can [create](#create), [update](#update), or [delete](#delete) a compute group.

### Creating a Compute Group

To create a new compute group:

- Choose "Create stack (with new resources)" in [CloudFormation](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)
- Then in the "Prepare template" section, choose "Template is ready" and proceed to [specify template](#specify-template)

### Updating a Compute Group

You can update a template to do one or both of the following:

- Upgrade to a newer version of Datomic
- Modify template parameters

To update a compute group template:

- Select an existing compute group stack in [CloudFormation](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)
- Then choose "Update stack"
- If you are upgrading Datomic, choose "Replace current template" and proceed to [specify template](#specify-template) below.
- If you are *only* modifying template parameters, choose "Use current template" and proceed to [parameters](#parameters) below.

### Deleting a Compute Group

Deleting a compute group deletes all that group's resources: EC2 instances, application load balancer, and API gateways. It does not delete your Datomic system or any data, and you can recreate the compute group again later. To delete a Datomic compute group:

- Find your compute group stack in [CloudFormation](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)
- Right-click on your query group stack and select "Delete stack"

## Specify Template

Datomic CloudFormation templates are stored in S3. To specify a compute template:

- Choose a template source of "Amazon S3 URL" or query group template
- Set the Amazon S3 URL to the latest primary compute template: [Primary Compute Template 1217-9399](https://s3.amazonaws.com/datomic-cloud-1/cft/1217/compute-template-9399-1217.json) or [Query Group Template 1217-9399](https://s3.amazonaws.com/datomic-cloud-1/cft/1217/query-group-template-9399-1217.json)

## Specify Stack Details

The compute group stack details include the stack name and the stack parameters.

### Stack Name

You will use the compute stack name to distinguish your compute group from other groups on the same system and from compute groups for other systems. A good choice is *$(System)-(Role)*, where *$(System)* is your system name, and *$(Role)* is the role this group serves, e.g. "primary compute", an application name, or a particular task or load profile. For example, the *inventory* system might have "inventory-primary" for its primary compute group, and "inventory-analytics" for a query group serving analytics.

### Parameters

Datomic configuration is managed via CloudFormation parameters, minimizing the surface area of AWS you must engage to run a system. As your needs grow, you may revisit and adjust the parameters that control the amount of compute resources, i.e. *Instance Type* and the *Auto Scaling Configuration*.

Most other parameters are likely to be set once when starting a compute group and never revisited. That said, except where explicitly noted, you can change any of these parameters by performing a rolling stack update.

Each parameter is summarized in the table below and then described in detail. Compute stack parameters are named by a `Label` in the CloudFormation UI, and under the `Parameters` key in CloudFormation JSON.

| Parameter (JSON) | Label (UI) | Value |
| --- | --- | --- |
| SystemName | System name | System name |
| InstanceType | Instance type | EC2 instance type |
| KeyName | AWS EC2 key pair | AWS keypair |
| ApplicationName | Application name | CodeDeploy application name |
| EnvironmentMap | Environment map | Ion application configuration |
| PreloadDatabase | Preload database | Database name |
| MetricsLevel | Metrics level | Default/none/basic/detailed |
| DesiredCapacity | Desired capacity | Number or "Default" |
| MinSize | Minimum instances | Number or "Default" |
| MaxSize | Maximum instances | Number or "Default" |
| MinInstancesInService | Minimum number of instances during update | Number or "Default" |
| ClientAPI | Client API | Yes/no |
| ClientApiConcurrentProcessing | Maximum number of concurrent Client API operations | Number or "Default" |
| Ions | Ions | Yes/no |
| NodePolicyARN | IAM managed policy for instances | IAM policy ARN |
| [OverrideSettings](../../../09-tech-notes/11-override-settings/override-settings.md) | Override settings | edn map |

- Instance type: type of EC2 instance for compute nodes. Check [instance sizes](../03-growing-your-system/growing-your-system.md#instance-sizes).
- AWS EC2 key pair: [key pair](../../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md#ec2-key-pair) for ssh access to nodes.
- Application name: [ion CodeDeploy application name](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#application-name). *This name cannot be changed later.*
- Environment map: ion [environment map](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#environment-map).
- Preload database: all compute group instances will load this database when they start, before accepting requests.
- Metrics Level: [level of metrics detail](../03-growing-your-system/growing-your-system.md#monthly-price). *Default* sets metric level [based on instance size](../03-growing-your-system/growing-your-system.md#instance-sizes), and should be suitable for most users.
- Auto scaling configuration: each of the auto scaling configuration settings allows you to set a specific number, or "Default" to choose a default based on your instance size.
  - Desired capacity: [AWS Auto Scaling desired capacity](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) or ["Default"](../03-growing-your-system/growing-your-system.md#instance-size-defaults)
  - Minimum group instances: [AWS Auto Scaling minimum size](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) or ["Default"](../03-growing-your-system/growing-your-system.md#instance-size-defaults).
  - Maximum group instances: [AWS Auto Scaling maximum size](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) or ["Default"](../03-growing-your-system/growing-your-system.md#instance-size-defaults).
  - The minimum number of instances during update: the number of instances that must be in service within the AWS while AWS CloudFormation updates instances, should be at least 1 less than maximum. A number or ["Default"](../03-growing-your-system/growing-your-system.md#instance-size-defaults).
- Client API: manage an AWS API gateway for Datomic Client API access. Most users should choose "yes". (If "no", you will have to configure VPC access for clients).
  - Concurrent Client API operations: maximum number of Client API operations a single node can process concurrently. You can set a specific number, or "Default" to choose a default based on your instance size.
- Ions: manage an AWS API Gateway for ion web applications. Most users should choose "yes". (If "no", HTTP direct ions will be inaccessible).
- Existing IAM managed policy for instances: *(Optional)* Name of a [IAM managed policy](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) to assign to instance nodes.
- Support configuration: leave blank unless directed by Datomic support.

## Configure Stack Options

The "configure stack options" screen has no Datomic-specific settings. Most users should accept all the defaults.

## Review

The review step does two things. It shows you the choices you made in the previous steps, and it has a *capabilities* section at the bottom where you must acknowledge the capabilities needed to run the stack.

When you create or update a Datomic compute stack, simply scroll down to the capabilities section, acknowledge the capabilities being used, and then create the stack.

## Wait for the stack

After you create or update a compute stack, CloudFormation will create and/or update AWS resources to match your specifications. This can take up to 25 minutes. You can refresh the CloudFormation dashboard to follow the stack's progress, which should end with `CREATE_COMPLETE` or `UPDATE_COMPLETE`.

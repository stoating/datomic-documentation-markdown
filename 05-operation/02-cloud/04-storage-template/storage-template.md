# Storage Template

This page is a reference for the Datomic Cloud storage CloudFormation template. It is organized to follow the steps in CloudFormation's web interface for creating or updating a stack:

- [Create or update](#create-or-update-stack)
- [Specify template](#specify-template)
- [Specify stack details](#specify-stack-details)
- [Configure stack options](#configure-stack-options)
- [Review](#review)
- [Monitoring a template](#monitoring-a-template)

## Create or Update Stack

To create a new storage stack, choose "Create stack (with new resources)". Then in the "Prepare template" section, choose "Template is ready".

To upgrade to a newer template release, select an existing storage stack. Then choose "Update Stack". In the "Prepare template" section, choose "Replace current template".

After the initial choice of create or upgrade, the subsequent steps are similar.

## Specify Template

Datomic CloudFormation templates are stored in S3. To specify a storage template:

- Choose a template source of "Amazon S3 URL"
- Set the Amazon S3 URL to the latest storage template: [CloudFormation Template 1217-9399](https://s3.amazonaws.com/datomic-cloud-1/cft/1217/storage-template-9399-1217.json)

## Specify Stack Details

CloudFormation stack details include the stack name and the stack parameters.

### Stack Name

The storage stack name is also the name of your Datomic system. Choose system names that are shorter than 24 characters and that make sense in compound names of the form "datomic-(system)-(resources)".

### Parameters

Storage stack parameters are named by a label in the CloudFormation UI, and by a parameter if you are reading the CloudFormation JSON:

| Parameter | Label | Value |
|-----------|-------|-------|
| Restart | Reuse Existing Storage | True or false |
| VpcCidrBlock | VPC CIDR Block | A CIDR block |
| Subnet0CidrBlock | First Subnet CIDR Block | A CIDR block |
| Subnet1CidrBlock | Second Subnet CIDR Block | A CIDR block |
| Subnet2CidrBlock | Third Subnet CIDR Block | A CIDR block |

Each parameter is described below.

#### Restart

The parameter `Restart` is labeled "Reuse Existing Storage" in the CloudFormation Template.

A Datomic storage stack can either create storage resources or reuse existing resources, based on the value of `Restart`. Datomic automatically manages storage resources, so start new systems with `false` (the default).

If you delete a Datomic storage stack, the underlying database storage is **not** deleted. You can recreate the storage stack later and continue using your data. If you are recreating a storage stack, (e.g. when splitting stacks) set `Restart` to `true`.

#### VPC Configuration Parameters

VPC configuration parameters control the assignment of private IP addresses in the Datomic VPC. The Datomic VPC as a whole uses the `VpcCidrBlock`. Within the block, Datomic will allocate three subnets in separate availability zones. Each subnet has its own `Subnet{N}CidrBlock`, which must be non-overlapping subsets of the `VpcCidrBlock`.

Datomic automatically creates and manages CIDR blocks for you. Datomic also manages gateways and load balancers so that you need never to connect to (or even care about the addresses of) individual compute nodes. Leave all VPC configuration parameters set to their defaults, unless you need [VPC peering](../09-vpc-access/vpc-access.md#inter-vpc).

## Configure Stack Options

The "Configure stack options" screen has no Datomic-specific settings. Use all the defaults.

## Review

The review step does two things. It shows the choices you made in the previous steps, and it has a "Capabilities" section at the bottom where you must acknowledge the capabilities needed to run the stack.

When starting a Datomic storage stack, you can simply scroll down to the Capabilities section, acknowledge the capabilities being used, and then create the stack.

## Monitoring a template

After you create or update a storage stack, CloudFormation will create and/or update resources so that your stack matches the template. This can take up to 25 minutes. You can refresh the CloudFormation dashboard to follow the stack's progress, which should end with `CREATE_COMPLETE` or `UPDATE_COMPLETE`.

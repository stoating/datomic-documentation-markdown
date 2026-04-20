# Start a System

This page contains instructions for creating a *split stack* Datomic
system, which provides operational flexibility by dividing the system
into separate storage and compute CloudFormation stacks.

## Checklist

To start a Datomic [system](../01-cloud-architecture/cloud-architecture.md#system):

- [Setup your AWS account](../../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md)
- [Choose a system name](#naming-systems-and-applications)
- [Create a storage stack](#create-a-storage-stack)
- [Create a compute stack](#create-a-compute-stack)

## Naming Systems and Applications

System and application names cannot be changed, so choose good names. Points to consider:

- Datomic creates AWS resources named "datomic-[system]-[subresource]", so keep your system name short - and don't name your system "datomic".
- Multiple systems might serve the same primary database in different development stages. So you might name systems via the convention "[db]-[stage]", e.g."inventory-dev", "inventory-staging" and "inventory-prod".
- A system can serve more than one application via different query groups, although one application is often primary. So you might name applications "[db](-[app])", e.g. "inventory" and "inventory-analytics".

## Create a Storage Stack

To create a storage stack from the [CloudFormation console](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active):

- Click "Create stack"
- Click "With new resources (standard)"
- Choose "Amazon S3 URL" as a template source
- Set the Amazon S3 URL to the latest:

```
https://s3.amazonaws.com/datomic-cloud-1/cft/1217/storage-template-9399-1217.json
```

You will need to choose a stack name, but you can generally leave all
stack parameters set to their default values. The [storage template
reference](../04-storage-template/storage-template.md) has further details.

## Create a Compute Stack

To create a compute stack from the
[CloudFormation console](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active):

- Click "Create stack"
- Click "With new resources (standard)"
- Choose "Amazon S3 URL" as a template source
- Set the Amazon S3 URL to the latest:

```
https://s3.amazonaws.com/datomic-cloud-1/cft/1217/compute-template-9399-1217.json
```

Unlike the storage stack, the compute stack has some parameters
that you should review and adjust to match your specific needs. See
the [compute template reference](../05-compute-templates/compute-templates.md) for details.

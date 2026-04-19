# Setup

The instructions on this page assume that you already have [set up your AWS account](../01-aws-account-setup/aws-account-setup.md).

To create a Datomic Cloud system, follow the steps below:

1. [Choose a system name](#naming-systems-and-applications)
2. [Create a storage stack](#create-a-storage-stack)
3. [Create a compute stack](#create-a-compute-stack)

These tasks need only be performed once, by an [AWS administrator](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html).

## Naming Systems and Applications

System and application names cannot be changed, so choose good names. Points to consider:

- Datomic creates AWS resources named "datomic-[system]-[subresource]", so keep your system name short – and **don't** name your system "Datomic".
- Multiple systems might serve the same primary database in different development stages. Name systems via the convention "[db]-[stage]", e.g. "inventory-dev", "inventory-staging" and "inventory-prod".
- A system can serve more than one application via different [query groups](../../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#query-groups), although one application is often primary. So you might name applications "[db](-[app])", e.g. "inventory" and "inventory-analytics".

## Create a Storage Stack

To create a storage stack from the [CloudFormation console](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active), follow the steps below:

1. Click "Create Stack".
2. Click "With new resources (standard)".
3. Choose "Amazon S3 URL" as a template source.
4. Set the Amazon S3 URL to the latest:

`https://s3.amazonaws.com/datomic-cloud-1/cft/1217/storage-template-9399-1217.json`

You will need to choose a stack name, but you can generally leave all stack parameters set to their default values. For further details, check the [storage template reference](../../../05-operation/02-cloud/04-storage-template/storage-template.md).

## Create a Compute Stack

To create a compute stack from the [CloudFormation console](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active), follow the steps below:

1. Click "Create stack".
2. Click "With new resources (standard)".
3. Choose "Amazon S3 URL" as a template source.
4. Set the Amazon S3 URL to the latest:

`https://s3.amazonaws.com/datomic-cloud-1/cft/1217/compute-template-9399-1217.json`

Unlike the storage stack, the compute stack has some parameters that you should review and adjust to match your specific needs. For more details, check the [compute template reference](../../../05-operation/02-cloud/05-compute-templates/compute-templates.md).

### Next Steps

If you are trying Datomic Cloud for the first time, a good next step is the [client API tutorial](../../../03-tutorials/02-client-tutorial/01-client-api/client-api.md).

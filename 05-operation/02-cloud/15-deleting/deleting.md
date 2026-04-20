# Deleting a System

Deleting a Datomic System requires two steps:

- [Deleting CloudFormation stacks](#deleting-cloudformation-stacks)
- [Permanently deleting durable storage](#deleting-durable-storage)

> Do **not** delete [region-wide shared resources](#region-wide-shared-resources).

## Deleting CloudFormation Stacks

Deleting the CloudFormation stacks associated with a Datomic system
removes all the active elements of the system (e.g. EC2 instances),
while leaving your data intact.

> You can delete CloudFormation stacks at any time, with no loss of
> data. You might do this e.g. to save computing costs for a system not
> in active use. To recreate the stacks later, simply follow the
> [recreate storage](../16-splitting-stacks/splitting-stacks.md#recreate-storage) and
> [recreate compute](../16-splitting-stacks/splitting-stacks.md#recreate-compute) instructions.

From your
[CloudFormation dashboard](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)
delete all stacks associated with the system as described below:

### Single Template System

If you followed the initial Setting Up instructions and have never
upgraded, your system will have a single top-level CloudFormation
stack whose name is the system name. Delete this stack.

### Multiple Template Systems

If you have ever upgraded the system, you will have multiple top-level
stacks. Delete the stacks in the order shown below, waiting for each
deletion to complete before continuing:

- Query Groups (in any order)
- Primary Compute
- Storage

## Deleting Durable Storage

> Note: These steps will permanently delete all databases and logs.

Replace (System) and (Region) in the instructions below with your system name:

- Delete the [S3](https://s3.console.aws.amazon.com/s3/home) bucket tagged with `datomic:system=(System)` in region `(Region)`
- Delete the [DynamoDB](https://console.aws.amazon.com/dynamodb/home) table in region `(Region)` named `datomic-(System)`
- Delete the [DynamoDB](https://console.aws.amazon.com/dynamodb/home) table in region `(Region)` named `datomic-(System)-catalog`
- Delete the [EFS](https://console.aws.amazon.com/efs/home) in region `(Region)` named `datomic-(System)`
- Delete the [CloudWatch](https://console.aws.amazon.com/cloudwatch/home#logs:) log group in region `(Region)` named `datomic-(System)`
- Deregister the read and write DynamoDB scalable targets for `datomic-(System)`
- Delete any [custom API Gateways](../08-customizing-api-gateways/customizing-api-gateways.md#creating-your-own-api-gateway). You will need to:
  - Go to the [API Gateway](https://console.aws.amazon.com/apigateway/main/apis)
  - Select your API
  - Click "Integrations"
  - Select each path then click Detach Integration
- Remove any [Custom Domain API Mappings](https://console.aws.amazon.com/apigateway/main/publish/domain-names?region=us-east-1) that are attached to API Gateways associated with the stack that you are deleting
  - Go to [Custom Domain Names](https://console.aws.amazon.com/apigateway/main/publish/domain-names?region=us-east-1)
  - Select the Domain associated with the API Gateway connected to the stack that you are deleting
  - Click the API Mappings tab
  - Click "Configure API mappings"
  - Click the "X" next to the API mappings to the stack(s) that you are deleting
  - Click "Save"

At the time of this writing, the "Deregister scalable targets" step is
not available in the AWS GUI console. Replace (Region) and (System) to
run the AWS CLI commands shown below.

```
aws --region (Region) application-autoscaling \
deregister-scalable-target --service-namespace dynamodb \
--scalable-dimension dynamodb:table:WriteCapacityUnits \
--resource-id table/datomic-(System)
```

```
aws --region (Region) application-autoscaling \
deregister-scalable-target --service-namespace dynamodb \
--scalable-dimension dynamodb:table:ReadCapacityUnits \
--resource-id table/datomic-(System)
```

## Region-wide Shared Resources

Datomic creates some resources that are shared by all systems in a
region. You should **not** delete these resources when deleting a system,
as you will disrupt other systems.

Delete these resources only after deleting all Datomic systems in
a region.

> The [ion code bucket](../11-how-to/how-to.md#find-ion-code-bucket) is tagged with `datomic:code`.

## Troubleshooting

If your stack deletion fails with `DELETE_FAILED`, the following list maps the CloudFormation status reason to a resolution:

#### Deleting API <uuid> failed. Please remove all API mappings for the API from your custom domain names

**Correcting the error**

- Go to the [API Gateway](https://console.aws.amazon.com/apigateway/main/apis)
- Click "Custom domain names"; you may have to open a left sidebar
- Select the custom domain
- Click the "API mappings" tab
- Click "Configure API mappings"
- Remove the API mapping that corresponds to the endpoint associated with the stack that you are deleting

#### resource sg-<uuid> has a dependent object

This error will include an additional status reason shown below. Extra values inside the `[]` indicate other potential errors:

```
The following resource(s) failed to delete: [LambdaSecurityGroup].
```

Or

```
The following resource(s) failed to delete: [DeleteDatomicLambdaEnis, LambdaSecurityGroup].
```

**Correcting the error**
This usually occurs when you've deployed an Ion to this system. To solve it:

- Copy the `sg-...` value in the error message
- Go to the [Network Interfaces Consoles](https://console.aws.amazon.com/ec2/v2/home)
- Search for the Security Group previously copied
  - Ensure that no other search filters are present
- Click a network interface in the list
- On the network interfaces page, check the *Description* and *Security* groups fields to verify that this network interface is part of the stack that you are deleting
- Click "Delete network interface"
- If you are certain that this is the network interface that you want to delete, click "Delete"
  - It can take up to 2 hours from the initial stack deletion attempt until the network interface can be deleted. If the deletion process fails, wait a bit then try again
- Go to [CloudFormation](https://console.aws.amazon.com/cloudformation/home) and delete the stack
  - Do not select to retain resources

**Avoiding the error**

> This must be done *BEFORE ATTEMPTING TO DELETE THE STACK* - If you have attempted to delete the stack, some of the resources necessary to deploy the Ion may have been deleted. This method will not work without the necessary resources to deploy an Ion. If you have already reached this error state, correct it with the previous instructions.

- Deploy an Ion with an `resources/datomic/ion-config.edn` of src_clojure `{:app-name "<app-name-here>"}`. Change `<app-name-here>` to your application name
- Go to [CloudFormation](https://console.aws.amazon.com/cloudformation/home) and delete the stack
- Leave the "Retain Resources" options unchecked

#### The following resource(s) failed to delete: [VpcLinkSecurityGroup, VpcLink].

This error occurs if you've created your own [API Gateway endpoint](../08-customizing-api-gateways/customizing-api-gateways.md) or VPC-based resources.

- Delete any [APIs](https://console.aws.amazon.com/apigateway/home) that are associated with the system that you do not wish to retain
- Delete any [VPCLinks](https://console.aws.amazon.com/apigateway/main/vpc-links/list) associated with the system
- Delete the stack in [Cloudformation](https://console.aws.amazon.com/cloudformation/home)

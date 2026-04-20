# Split CloudFormation Stacks

This page explains how to convert a master stack system into a split stack system.

## Rationale

> This section only applies to [Datomic 990-9202](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#990-9202) and lower.
> Newer versions of Datomic Cloud do not use the Master stack templates.

A Datomic Cloud system comprises at least 2 CloudFormation stacks per
[AWS best practice guidelines](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html#organizingstacks):

- One storage resource stack
- One or more compute stacks

These stacks are nested under a
master stack. The master stack makes various operational tasks more
difficult. For production operation, use a *split stack* system,
i.e. separate top-level storage and compute stacks.

There are two ways to run a split stack system:

- Create a [split stack](../02-start-a-system/start-a-system.md) system from scratch
- Convert a [master stack](../02-start-a-system/start-a-system.md) system into a split stack system, as the instructions below

## How to Split Datomic Stacks

The following steps convert a Datomic system from the
master stack setup to the split stack setup. There are two
steps:

- [Delete the master stack](#delete-the-master-stack)
- [Recreate the stacks](#recreating-stacks)

### Delete the Master Stack

Deleting the master stack will make your system temporarily unavailable, but does not harm your data:

- Select the root stack for your system in the
[CloudFormation console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active).
The root stack will have a *Stack Name* that is the same as your [system name](../11-how-to/how-to.md#find-datomic-system-name).
- Click "Delete" from the menu bar. Confirm this in the Delete Stack popup, then wait for the stack deletion to
complete. This can take 10 minutes or more.

### Recreating Stacks

The process of recreating the stacks is the same as [starting a new split stack system](../02-start-a-system/start-a-system.md#create-a-storage-stack) with the following exceptions:

- Storage stack name is the name of the system that you just deleted
- Reuse existing storage must be set to "True"

The [split stack instructions](../02-start-a-system/start-a-system.md#create-a-storage-stack) are annotated where necessary for recreating your stack.

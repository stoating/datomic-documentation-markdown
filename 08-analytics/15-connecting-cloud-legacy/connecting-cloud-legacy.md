# Connecting

This page covers:

- [Connecting to the access gateway](#connect)
- [Testing the analytics connection](#test)

## Prerequisites

This page assumes that an administrator has already:

- [Started a Datomic System](../../05-operation/02-cloud/02-start-a-system/start-a-system.md)
- [Configured user access](../../05-operation/02-cloud/06-access-control/access-control.md)
- [Configured analytics](../03-cloud-configuration/cloud-configuration.md)

Before you connect, you need to:

- Install the [AWS CLI](../../05-operation/02-cloud/11-how-to/how-to.md#install-cli) version 1.11.170 or greater
- Install the [Datomic CLI tools](../../05-operation/02-cloud/07-cli-tools/cli-tools.md)
- Configure a shell environment with [AWS access keys](../../05-operation/02-cloud/11-how-to/how-to.md#aws-access-keys) for Datomic
- Know the [region and name](../../05-operation/02-cloud/11-how-to/how-to.md#system-name) of the Datomic system you want to connect to

## Connecting

Use the [Datomic tools](../../05-operation/02-cloud/07-cli-tools/cli-tools.md#analytics) to create an analytics connection, passing in the arguments `analytics` and your Datomic [system name](../../05-operation/02-cloud/11-how-to/how-to.md#system-name).

```
datomic analytics access <system-name>
```

## Testing Your Connection

Test your analytics connection in two different ways:

- Browse to the analytics dashboard by visiting [localhost:8989](http://localhost:8989) in a web browser. A monitoring dashboard will be displayed.
- Install the [SQL CLI](../04-sql-cli/sql-cli.md) and use it to validate your configuration.

Once you have verified your connection via the dashboard and the SQL CLI, you can explore your data from the CLI or set up and run the [analytics tools](../analytics.md) of your choice.

# Setup

## Clone the Sample Project

```sh
git clone https://github.com/Datomic/ion-starter.git
```

The [ion-starter](https://github.com/Datomic/ion-starter) project contains a complete ion-based application. To begin the tutorial, clone the ion-starter project.

## Set the Application Name

Ions are deployed with an [application name](../02-ions-reference/ions-reference.md#application-name) that must match your compute group.

Edit the `resources/datomic/ion-config.edn` file and set the `:app-name` to your application name.

> Multiple compute groups can share the application name. The compute groups that you can deploy to, for that application, are displayed after a successful [push operation](../03-ions-tutorial-introduction/push-and-deploy.md#push).

## Install the Ion-Dev Tools

Make sure you have [installed the ion-dev tools](../../05-operation/02-cloud/11-how-to/how-to.md#ion-dev) before [deploying](../03-ions-tutorial-introduction/push-and-deploy.md), learn to [develop at the REPL](../03-ions-tutorial-introduction/develop.md).

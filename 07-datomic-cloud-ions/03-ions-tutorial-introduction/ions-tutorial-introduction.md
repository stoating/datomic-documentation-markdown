# Ions Tutorial

This tutorial shows how to write applications that run entirely inside Datomic Cloud without any additional servers.

With ions, you can build:

- [Web services accessed](../07-entry-points/entry-points.md#http-direct) via AWS API Gateway for use by other applications or single page web apps
- Data-driven applications that [are invoked](../07-entry-points/entry-points.md#invoke-a-lambda) via AWS lambda

In this tutorial, you will deploy a simple inventory application with functions to add and query inventory, and then expose this application via both Lambda and AWS API Gateway. Along the way, you will learn how to develop, [push](../06-push-and-deploy/push-and-deploy.md#push), [deploy](../06-push-and-deploy/push-and-deploy.md#deploy) and [use](../07-entry-points/entry-points.md) your functions via:

- [API gateway HTTP direct](../07-entry-points/entry-points.md#http-direct)
- [AWS lambda](../07-entry-points/entry-points.md#invoke-a-lambda)
- [Datomic attribute and entity predicates, query functions, and transaction functions](../07-entry-points/entry-points.md#add-an-attribute-predicate)

The [Day of Datomic Cloud](https://youtu.be/qplsC2Q2xBA?t=1371) and [Datomic ions](https://www.youtube.com/watch?v=3BRO-Xb32Ic) videos discuss use of an Ion, with the code [on Github](https://github.com/Datomic/ion-event-example). Some parts of these videos reference workflows or technologies that may not apply in Datomic 884-9095 and later.

## Tutorial Prerequisites

Before this tutorial, perform the following:

- Install Datomic Cloud Version 884-9095 or later. Follow the instructions to [create](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md) a new Datomic Cloud system, or to [upgrade](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md) an existing system
- Run a [compute group](../../05-operation/02-cloud/05-compute-templates/compute-templates.md) with parameter [Ions="yes"](../../05-operation/02-cloud/05-compute-templates/compute-templates.md#parameters)
  - Know the [app name of your system](../../05-operation/02-cloud/11-how-to/how-to.md#find-datomic-application-name)
- Read the [ions overview](../01-ions-overview/ions-overview.md)
- Know how to launch and use a [Clojure CLI REPL](../../05-operation/02-cloud/11-how-to/how-to.md#install-clojure-cli)
- Know how to work with a git repository
- Run in an environment with [proper AWS access keys](../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md#ec2-key-pair)
- Have [AWS administrator](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html#jf_administrator) permissions

You are now ready to [setup your first ion](../04-setup/setup.md).

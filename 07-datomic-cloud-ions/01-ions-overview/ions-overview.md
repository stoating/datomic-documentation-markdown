# Datomic Cloud Ions

Ions lets you develop applications for the cloud by deploying your code to a running Datomic Cloud compute group. You can **focus on your application logic**, write ordinary Clojure functions, and the ion tooling and infrastructure handles the deployment and execution details. It's possible to leverage your code both inside Datomic transactions and queries — and from the world at large via built-in support for AWS Lambda and web services.

There are four activities involved in using Datomic ions:

- [Dev](#how-dev): write your application as a set of ordinary Clojure functions
- [Push](#how-push): capture your current local view of the world as a *revision* you can later deploy
- [Deploy](#how-deploy): a revision to a Datomic compute group
- [Bond](#how-bond): use the functions you deployed in transactions, queries, AWS Lambdas, or web services

## Benefits

- Focus on your application
- Leverage your Datomic Cloud cluster compute resources and data locality
- Extend Datomic transactions and queries with your own logic
- Connect to the broader AWS cloud via [lambda events](https://docs.aws.amazon.com/lambda/latest/dg/lambda-services.html)
- Service web consumers with API gateway
- Scale Datomic and your app together
- Deliver on AWS with high agility

![ions-who-do.png](https://docs.datomic.com/images/ions-who-do.png)

## Activities

### Dev

You develop ions in an ordinary [tools.deps](https://clojure.org/guides/deps_and_cli) based Clojure project kept in git. You can have Git and Maven dependencies, and even local deps of libs in progress.

Ions include one or more [entry points](../02-ions-reference/ions-reference.md#entry-points). These are Clojure functions that have a signature matching their intended use:

- Transaction functions take and return transaction data
- Query expressions take a return of arbitrary data
- Lambdas take and return JSON payloads. Ion lambdas can handle [AWS events](https://docs.aws.amazon.com/lambda/latest/dg/invoking-lambda-function.html), or you can invoke them directly
- Web service functions support [ring-like edn handlers](../02-ions-reference/ions-reference.md#web-ion)

You can develop and test these entry points (and all their supporting code) as ordinary Clojure functions at the REPL.

### Push

You capture the current state of your application code by invoking [push](../02-ions-reference/ions-reference.md#push). *push* creates a named CodeDeploy revision in Amazon S3, which you can later deploy to one or more Datomic [compute groups](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#compute-groups). If you are working on committed code with no local deps you will get a reproducible revision named after your commit. For work in progress, supply a name for your (unreproducible) revision.

*push* reads your deps.edn and ensures all library dependencies and local code are moved to S3. In addition, it creates a first-class [revision](https://docs.aws.amazon.com/codedeploy/latest/userguide/application-revisions.html) in Code Deploy. Push is smart about minimizing the transfer of code to S3.

### Deploy

When you create a [primary compute group](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#compute) or query group, you associate it with a [Code Deploy](https://aws.amazon.com/codedeploy/) application. The `:app-name` in your ion-config.edn connects your code to a Code Deploy application and determines which compute groups you can deploy to.

Datomic Cloud manages a *code deploy* [deployment group](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html) for each compute group. The [ion deploy](../02-ions-reference/ions-reference.md#deploy) command deploys a previously-pushed ion revision to a deployment group.

*deploy* uses AWS [step functions](https://aws.amazon.com/step-functions/) to:

- Deploy your code and dependencies to the compute group using Code Deploy. Code Deploy works by moving code from S3 to the compute group's EC2 instances and cycling the Datomic process with a newly-extended classpath, in a rolling fashion. This is much faster than cycling EC2 instances. Deploy is smart about minimizing the transfer of code from S3.
- [Optionally] ensure Lambdas If you've configured Lambdas, deploy ensures an AWS Lambda corresponding to each [lambda entry point](../02-ions-reference/ions-reference.md#lambda-ion). These AWS Lambdas are in fact just lightweight proxies that forward invocations to your functions running on the Datomic cluster (i.e. your code is **not** running in AWS Lambda). In this way, your code runs near the data and cache, without the limitations and complexities of running in the Lambda execution context.

### Bond

Ions include an [ion-config.edn file](../02-ions-reference/ions-reference.md#ion-config) that specifies how to invoke your ion code:

- As [transaction functions](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md#custom) inside a Datomic transaction
- As [query expressions](../../06-reference/03-query-and-pull/query-reference.md#calling-clojure-functions) inside a Datomic query
- As [lambda functions](../02-ions-reference/ions-reference.md#invoke-lambda), either directly or in response to AWS events
- As web services via AWS API Gateway and either [HTTP direct](../02-ions-reference/ions-reference.md#http-direct-config) or a [lambda proxy](../02-ions-reference/ions-reference.md#lambda-config)

## Ion Developer Resources

- [Ions tutorial](../03-ions-tutorial-introduction/ions-tutorial.md)
- [Ions reference](../02-ions-reference/ions-reference.md)
- Rich Hickey's [Clojure/nyc presentation](https://www.youtube.com/watch?v=thpzXjmYyGk) (1:45)
- Stuart Halloway's [Ions in seven minutes](https://www.youtube.com/watch?v=TbthtdBw93w) (0:07)

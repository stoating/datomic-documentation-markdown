# Ions Reference

This page covers the basic steps of [ion](../01-ions-overview/ions-overview.md) development:

- [Prerequisites](#prerequisites)
- [Developing ions](#developing-ions)
- Packaging ions with [push](#push)
- [Deploying ions](#deploy) to a running compute group
- [Invoking ions](#invoking-ions)

It also covers more advanced topics:

- [Configuring compute groups](#configuring-compute-groups)
- [Naming applications](#naming-applications)
- [Best practices](#best-practices)

## Prerequisites

Before you begin developing an ion application, you will need:

- A Datomic system using a [split stack](../../05-operation/02-cloud/16-splitting-stacks/splitting-stacks.md)
- The Clojure [CLI](../../05-operation/02-cloud/11-how-to/how-to.md#install-clojure-cli)
- The [ion-dev tools](../../05-operation/02-cloud/11-how-to/how-to.md#install-ion-dev-tools)
- [Git](https://git-scm.com)

## Developing Ions

This section covers the things you need to know when writing ion code:

- Ion [project structure](#project-structure)
- [Using the Client API from ions](#ion-clients)
- Implementing ion [entry points](#ion-entry-points)
- [Configuring entry points](#ion-config)
- Ion [JVM settings](#jvm-settings)

### Project Structure

You develop ions in a tools.deps project organized as follows:

- The project must be a git repository – Ions use git SHAs to uniquely name a reproducible revision.
- There should be a single deps.edn file located at the project root. Multiple deps.edn files are incompatible with the use of SHAs to uniquely name a revision.
- The `:deps` sections of deps.edn must include the [client-cloud](../../02-accessing/02-client-library/client-library.md#installing-the-client-library) and [ion](../../05-operation/02-cloud/11-how-to/how-to.md#install-ion-library) libraries.
- The deps.edn classpath must include a resource named `datomic/ion-config.edn`. This resource configures the [entry points](#ion-entry-points) for an application.

For an example, check the sample project [deps.edn](https://github.com/Datomic/ion-starter/blob/master/deps.edn) and [ion-config.edn](https://github.com/Datomic/ion-starter/blob/master/resources/datomic/ion-config.edn).

### Ion Clients

In an ion application, your client code and Datomic run in the same process on a [compute node](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#nodes). To support this while also supporting development and testing, you can use the `:ion` server type as an argument to the [client](../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#client). When you create a client with `:ion` server-type, Datomic will create a client based on where your code is running:

- If the code is running on a Cloud node, Datomic will ignore the rest of the argument map to `client` and create an in-memory ion client
- Otherwise, Datomic will use the rest of the argument map to create a client as for the [cloud server-type](../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#client)

> Clients created with the `:ion` server type support the [synchronous client API](../../04-apis/04-client-api/client-api.md#synchronous-api) only.

The following example shows how to create a client that connects to the inventory-dev system in us-east-1 during development. When deployed as an ion, the same code will create an in-memory client on the system it is deployed to.

```clojure
(require '[datomic.client.api :as d])

(def cfg {:server-type :ion
          :region "us-east-1"
          :system "inventory-dev"
          :endpoint "https://ljfrt3pr18.execute-api.us-east-1.amazonaws.com"})

(def client (d/client cfg))
```

### Ion Entry Points

Ion applications are arbitrary Clojure code, exposed to consumers via one or more *entry points*. An entry point is a function with a well-known signature. There are five types of entry points for different callers, each with a different function signature.

[Lambda](#lambda-entry-point) and [HTTP direct](#http-direct-entry-point) are external entry points. They expose AWS lambdas and web services, respectively.

Internal entry points are callbacks that extend the [Datomic Client API](../../04-apis/04-client-api/client-api.md) with your code. They include [transaction functions](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md), [query functions](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#functions), and [pull xforms](../../06-reference/03-query-and-pull/03-pull/pull.md#xform-option).

| Entry point | Called from | Input | Output |
|---|---|---|---|
| [Lambda](#lambda-entry-point) | AWS lambda | `:input` JSON, `:context` map | String, InputStream, ByteBuffer, or File |
| [HTTP Direct](#http-direct-entry-point) | Web client | Web request | Web response |
| [Transaction fn](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md) | Inside a transaction | Transaction data | Data |
| [Query fn](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#functions) | Inside query | Data | Data |
| [xform](../../06-reference/03-query-and-pull/03-pull/pull.md#xform-option) | Pulled attribute value | Data | Data |

#### Lambda Entry Point

A lambda entry point is a function that takes a map with two keys:

- `:input` is a String containing the input JSON payload.
- `:context` contains all the data fields of the [AWS lambda context](https://github.com/aws/aws-lambda-java-libs/blob/9c0947ab44fc9302dc2d373e3d5a94e673941521/aws-lambda-java-core/src/main/java/com/amazonaws/services/lambda/runtime/Context.java) as a Clojure map, with all keys (including nested and [dynamic map](https://github.com/aws/aws-lambda-java-libs/blob/9c0947ab44fc9302dc2d373e3d5a94e673941521/aws-lambda-java-core/src/main/java/com/amazonaws/services/lambda/runtime/ClientContext.java#L19-L31) keys) converted to keywords.

A lambda entry point can return any of String, ByteBuffer, InputStream, or File; or it can throw an exception to signal an error.

For example, the following `echo` function simply echos back its invocation:

```clojure
(defn echo
  [{:keys [context input]}]
  input)
```

Ion [deploy](#deploy) automatically creates AWS lambdas that forward requests to your entry points. Your entry points have access to all the resources of your Datomic system and are not constrained by the execution context of AWS lambdas. However, entry point responses must still flow back through AWS lambda to callers, so lambda entry points *are* bound by the [AWS lambda limits](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) on response size (6MB) and response timeout (900 seconds).

#### HTTP Direct Entry Point

A web entry point is a function that takes the following input map:

| Key | Req'd | Value | Example |
|---|---|---|---|
| `:body` | No | InputStream | "hello" |
| `:headers` | Yes | Map string->string | {"x-foo" "bar"} |
| `:protocol` | Yes | HTTP protocol | "HTTP/1.1" |
| `:remote-addr` | Yes | Caller host | "example.com" |
| `:request-method` | Yes | HTTP verb as keyword | `:get` |
| `:scheme` | No | `:http` or `:https` | `:https` |
| `:server-name` | Yes | Server host name | "example.com" |
| `:server-port` | No | TCP port | 443 |
| `:uri` | Yes | Request URI | `/` |

And returns:

| Key | Req'd | Value | Example |
|---|---|---|---|
| `:body` | No | InputStream or String | "goodbye" |
| `:headers` | Yes | Map string->string | {"x-foo" "bar"} |
| `:status` | Yes | HTTP status code | 200 |

The input and output maps are a subset of the Clojure [ring spec](https://github.com/ring-clojure/ring), and many web applications should be able to run unmodified as HTTP direct ions.

The ion-starter project includes an example [HTTP direct ion](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter/http.clj).

### ion-config

The `datomic/ion-config.edn` resource configures the application's [entry points](#ion-entry-points). Ion-config is an EDN map with the following keys:

- `:app-name` is string name of a Datomic application.
- `:allow` is a vector of fully qualified symbols naming [query](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#calling-clojure-functions) or [transaction](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md) functions. When you deploy an application, Datomic will automatically require all the namespaces mentioned under `:allow`.
- `:xforms` is a vector of fully qualified symbols naming functions for use in [xform](../../06-reference/03-query-and-pull/03-pull/pull.md#xform-option).
- `:lambdas` is a map configuring [AWS lambda entry points](#lambda-entry-point)
- `:http-direct` is a map configuring an [HTTP direct entry point](#http-direct-entry-point)

#### `:lambdas`

`:lambdas` is a map from lambda names (keywords) to lambda configurations.

For each entry in the `:lambdas` map, [ion deploy](#deploy) will create an AWS lambda named `$(group)-$(name)`, where `group` is the `:group` key you used to invoke `deploy`, and `name` is the name in the lambda map. AWS lambda names are limited to 64 characters, so make sure that you choose a lambda name that will not exceed this when appended to your group name.

A lambda configuration supports the following keys:

| Key | Required | Value | Example | Default |
|---|---|---|---|---|
| `:fn` | Yes | Symbol | `datomic.ion.starter/echo` | |
| `:description` | No | String | "echo" | "" |
| `:timeout-secs` | No | Positive int | 60 | 60 |
| `:concurrency-limit` | No | Positive int or `:none` | 20 | 20 |
| `:integration` | No | `:api-gateway/proxy` | `:api-gateway/proxy` | |

`:concurrency-limit` sets [AWS reserved concurrency](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html). Set it `:none` if you want the AWS lambda to use unreserved concurrency.

The `:integration` key customizes integration between the lambda and AWS. The only value currently supported is `:api-gateway/proxy`. Set this only for [web Lambda proxies](#web-lambda-proxies).

#### HTTP Direct Configuration

The HTTP Direct configuration map supports the following keys:

| Key | Required | Description | Default |
|---|---|---|---|
| `:handler-fn` | Yes | Fully qualified handler function name | |
| `:pending-ops-queue-length` | No | Number of operations to queue | 100 |
| `:processing-concurrency` | No | Number of concurrent operations | 16 |
| `:pending-ops-exceeded-message` | No | Return message when queue is full | "Throttled" |
| `:pending-ops-exceeded-code` | No | HTTP status code returned when queue is full | 429 |

Nodes will enqueue up to `:pending-ops-queue-length` HTTP direct requests, servicing them with `:processing-concurrency` threads. When the ops queue is full, nodes will reject requests with `:pending-ops-exceeded-messsage` and `:pending-ops-exceeded-code`.

You can use the `HttpDirectOpsPending` and `HttpDirectThrottled` [metrics](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud) to monitor your application and [autoscale query groups](../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md#adding-and-scaling-a-query-group).

#### Ion-Config Example

The example below is taken from the [ion-starter project](https://github.com/Datomic/ion-starter/blob/master/resources/datomic/ion-config.edn):

```clojure
{:allow [;; transaction function
         datomic.ion.starter/create-item

         ;; query function
         datomic.ion.starter/feature-item?]

 ;; AWS Lambda entry points
 :lambdas {:echo
           {:fn datomic.ion.starter/echo
            :description "Echos input"}
           :get-tutorial-schema
           {:fn datomic.ion.starter/get-tutorial-schema
            :description "returns the schema for the Datomic docs tutorial"}
           :get-items-by-type
           {:fn datomic.ion.starter/get-items-by-type
            :description "Lambda handler that returns items by type"}}

 ;; HTTP Direct entry point
 :http-direct {:handler-fn datomic.ion.starter/items-by-type}
 :app-name "<YOUR-APP-HERE>"}
```

### JVM Settings

When developing and testing locally, it can be helpful to match the JVM settings that your code will run under in Datomic Cloud.

| Instance type | Heap (-Xmx) | Stack (-Xss) |
|---|---|---|
| `t3.small` | 1189m | 512k |
| `t3.medium` | 2540m | 1m |
| `t3.large` | 5333m | 1m |
| `t3.xlarge` | 10918m | 1m |
| `i3.large` | 10520m | 1m |
| `i3.xlarge` | 21280m | 1m |

Additionally, all instances use the following flags:

```text
-XX:+UseG1GC -XX:MaxGCPauseMillis=50 -Dclojure.spec.skip-macros=true
```

## Push

Ion /push/package your application into runnable artifacts. Push creates an AWS CodeDeploy application revision in S3 that can later be [deployed](#deploy) to one or more [compute groups](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#compute-groups). Ion push understands your dependencies at a granular level, so e.g. all revisions can share the same copy of a common library. This is more efficient than "uberjar" style deployment.

To push an application revision, call datomic.ion.dev with an `:op` of `:push`:

```sh
clojure -A:ion-dev '{:op :push (options)}'
```

| Keyword | Required | Value | Example |
|---|---|---|---|
| `:op` | Yes | `:push` | `:push` |
| `:uname` | No | [Unreproducible name](#unreproducible-push) | "janes-wip" |
| `:creds-profile` | No | [AWS profile name](../../05-operation/02-cloud/11-how-to/how-to.md#manage-aws-access-keys-for-datomic) | "janes-profile" |
| `:region` | No | [AWS region](../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md#supported-regions) | "us-east-1" |

Push will ensure that your application's dependencies (Git and Maven libs) exist in your [ion code bucket](../../05-operation/02-cloud/11-how-to/how-to.md#find-ion-code-bucket):

```text
s3://$(ion-code-bucket)/datomic/libs
```

And will create an application revision located at:

```text
s3://$(ion-code-bucket)/datomic/apps/$(app-name)/$(git-sha|uname).zip
```

On success, push returns a map with the following keys:

| Keyword | Required | Value |
|---|---|---|
| `:rev` | No | Git SHA for the commit that was pushed |
| `:uname` | No | [Unreproducible name](#unreproducible-push) for the push |
| `:deploy-groups` | Yes | List of available groups for deploy |
| `:deploy-command` | Yes | Sample command for [deploy](#deploy) |
| `:doc` | No | Documentation |
| `:dependency-conflicts` | No | Map describing [conflicts](#dependency-conflicts) |

### Dependency Conflicts

Because ions run on the same classpath as Datomic Cloud, it is possible for ion dependencies to conflict with Datomic's own dependencies. If this happens:

- Datomic's dependencies will be used
- The return from push will warn you with a `:dependency-conflicts` map
- You can add the `:deps` from the conflicts map to your local project so that you can test against the libraries used by Datomic Cloud

The Datomic team works to keep Datomic's dependencies up-to-date. If you are unable to resolve a dependency conflict, please [contact support](https://www.datomic.com/support.html).

### Unreproducible Push

By default, an ion push is *reproducible*, i.e. built entirely from artifacts in git or maven repositories. For a push to be reproducible, your git working tree must be clean, and your deps.edn project cannot include any [local/root dependencies](https://clojure.org/reference/deps_and_cli#_dependencies). A reproducible push is uniquely named by the SHA of its git commit.

In some situations, you may want to push code that Datomic does not know to be reproducible. For example:

- You are testing work-in-progress that does not have a git commit
- You are implementing your own approach to reproducibility

For these situations, ions permit an *unreproducible* push. Since an unreproducible push has no git SHA, you must specify an *uname* (`:uname`).

You are responsible for making the uname unique within your [application](#naming-applications). If unames are not unique, ions will be unable to automatically roll back failed deploys.

## Deploy

Ion deploy deploys a [pushed revision](#push) to a compute group.

When you deploy, Datomic will use AWS [Step Functions](https://aws.amazon.com/step-functions/) to:

- CodeDeploy the code for the application onto each instance in the compute group.
- Automatically roll back to the previous deployment if the application does not deploy correctly (e.g. if loading a namespace throws an exception).
- Ensure that active databases are present in memory before routing requests to newly updated nodes.
- Ensure that all the lambdas requested via the ion-config.edn map exist and are configured correctly.

Node deployment happens one node at a time, so a Datomic system will remain available with N-1 members active throughout a deployment.

To deploy an application, call datomic.ion.dev with an `:op` of `:deploy`:

```sh
clojure -A:ion-dev '{:op :deploy (options)}'
```

| Keyword | Required | Value | Example |
|---|---|---|---|
| `:op` | Yes | `:deploy` | `:deploy` |
| `:group` | Yes | [Compute group name](../../05-operation/02-cloud/11-how-to/how-to.md#find-compute-group-name) | "my-datomic-compute" |
| `:rev` | (Or `:uname`) | Output from [push](#push) | "6468765f843d70e01a7a2e483405c5fcc9aa0883" |
| `:uname` | (Or `:rev`) | Input to [push](#push) | "janes-wip" |
| `:creds-profile` | No | [AWS profile name](../../05-operation/02-cloud/11-how-to/how-to.md#manage-aws-access-keys-for-datomic) | "janes-profile" |
| `:region` | No | [AWS region](../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md#supported-regions) | "us-east-1" |

The **time to complete** an entire deployment is the sum of:

- the sum of times to deploy to each node
- the time to (re)configure lambdas

The **time to deploy** to a single node is the sum of:

- the time to copy new code and deps from S3 to the node
- the time to stop and restart the Datomic Cloud process
- the time to load active databases into memory

Deployment to a single node can take from 20 seconds up to several minutes, depending on the number and size of active databases.

You can monitor a deployment with the [deploy-status command](#deploy-status) or [from the AWS Console](#console-status).

#### Deploy-Status

To check the status of a [deploy](#deploy), call datomic.ion.dev with an `:op` of `:deploy-status`:

```sh
clojure -A:ion-dev '{:op :deploy-status ...}'
```

| Keyword | Required | Value | Example |
|---|---|---|---|
| `:op` | Yes | `:deploy-status` | `:deploy-status` |
| `:execution-arn` | Yes | Output from [deploy](#deploy) | "arn:aws:states:us-east-1:123456789012:execution:datomic-compute:datomic-compute-1526506240469" |
| `:creds-profile` | No | AWS profile name | "janes-profile" |
| `:region` | No | [AWS region](../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md#supported-regions) | "us-east-1" |

Deploy status returns the [Step Functions reference](https://docs.aws.amazon.com/step-functions/latest/apireference/API_DescribeExecution.html#StepFunctions-DescribeExecution-response-status) of the overall ion deploy and the code deploy as a map:

```clojure
{:deploy-status "SUCCEEDED"
 :code-deploy-status "SUCCEEDED"}
```

On success, both the overall ion deploy and code deploy status will eventually return with "SUCCEEDED".

#### Console Status

You can monitor a [deploy](#deploy) in near real-time from the [Step Functions Console](https://console.aws.amazon.com/states/home?#/statemachines). Look for a state machine named `datomic-$(group)`.

The CodeDeploy step can be monitored in the [CodeDeploy Deployments console](https://console.aws.amazon.com/codesuite/codedeploy/deployments).

## Invoking Ions

Ion entry points that extend the power of the Client API are described in the relevant documentation sections:

- [Transaction functions](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md)
- [Query functions](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#functions)
- [Xforms](../../06-reference/03-query-and-pull/03-pull/pull.md#xform-option)

Web service and lambda invocations are described below.

### Invoking Web Services

Ion web services are exposed at the URL `https://$(IonApiGatewayEndpoint)`, where `IonApiGatewayEndpoint` is in your compute group's [template outputs](../../05-operation/02-cloud/11-how-to/how-to.md#find-template-outputs). You can invoke a web service with any HTTPS client, e.g. curl:

```sh
curl https://$(IonApiGatewayEndpoint)
```

If your compute template does not have an `IonApiGatewayEndpoint`, see [older versions of Datomic](#older-versions-of-datomic-cloud).

### Invoking Lambdas

When you deploy an application with lambda ions, Datomic will create AWS lambdas named:

```text
$(group)-$(name)
```

Where `group` is the `:group` key you used to invoke `deploy`, and `name` is the name under the `:lambdas` key in [ion-config.edn](#ion-config).

The tutorial includes an [example](../07-entry-points/entry-points.md#invoke-a-lambda) of invoking an ion Lambda via the AWS CLI.

## Configuring Compute Groups

Ions typically have some amount of configuration, e.g. the name of a Datomic database that they use. If you are deploying your ion application to a single compute group, then you might choose to keep such configuration in Clojure vars in your application source code. This is easy, if not simple:

- Everything is in a single place. For small services, it may even fit in a single screen of Clojure code.
- Code and configuration are versioned together on every ion push.

If you have more complex ions, potentially deployed to multiple compute groups (or even multiple systems), there are several challenges to consider:

- Configuration values may have a lifecycle that is independent of application source code. They should not be hard-coded in the application and should be managed separately from the source code.
- Applications need a way to obtain their configuration values at runtime.
- Configuration values may be sensitive, and should not be stored or conveyed as plaintext.
- Configuration values may need to be secured at a granular level.

Datomic Cloud provides tools to help with these challenges.

- The compute group [app-info map](#app-info-map) describes the compute group
- The compute group [environment map](#environment-map) lets you add your own configuration, per compute group
- [Parameters](#parameters) allow you to create a configuration with arbitrary scope and lifecycle, and [secure them at fine granularity with IAM](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-access.html)

| Configuration | Scope | Source | IAM |
|---|---|---|---|
| App-info map | Compute group | Datomic | No |
| Environment map | Compute group | User | No |
| Parameters | Arbitrary | User | Yes |

### App-Info Map

If you deploy the same ion to different compute groups, your code may want to be conditional based on which group it is running in. You can discover your compute group at runtime with `get-app-info`, which takes no arguments, and returns a map that will include at least these keys:

- `:app-name` is your [app-name](#ion-config)
- `:deployment-group` is the name of your ion [deployment group](#deploy)

For example, the following excerpt from [deploy-monitor](https://github.com/Datomic/ion-event-example/blob/master/src/datomic/ion/event_example.clj) gets the app-name:

```clojure
(get (ion/get-app-info) :app-name)
```

When running outside Datomic Cloud, `get-app-info` returns the value of the `DATOMIC_APP_INFO_MAP` environment variable, read as EDN.

If you want to add your own per-compute-group configuration, you can create an environment map.

### Environment Map

You create an environment map when you first [create a compute group](../../05-operation/02-cloud/05-compute-templates/compute-templates.md#stack-name). For example, the environment map below specifies that a compute group is for a `:staging` deployment environment:

![environment-map.png](https://docs.datomic.com/images/environment-map.png)

You can then retrieve the environment map at runtime with `get-env`, which takes no arguments, and returns the [environment map](#environment-map).

For example, the following excerpt from [deploy-monitor](https://github.com/Datomic/ion-event-example/blob/master/src/datomic/ion/event_example.clj) retrieves the deployment environment specified above:

```clojure
(get (ion/get-env) :env)
```

You can set the environment map for local development via the `DATOMIC_ENV_MAP` environment variable. When running outside Datomic Cloud, `get-env` returns the value of `DATOMIC_ENV_MAP`, read as EDN.

If you need to change the environment map for a running compute group, you can [update the group's CloudFormation stack](../../05-operation/02-cloud/05-compute-templates/compute-templates.md#updating-a-compute-group).

If you need a more flexible lifecycle or granular security, you can use parameters as described below.

### Parameters

- A *parameter* is a named slot known to application code that can be filled in with a specific *parameter value* at runtime
- The [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-paramstore.html) provides an implementation of parameters that support an independent lifecycle, encryption for sensitive data, and IAM security over a hierarchical naming scheme
- Inside a given AWS account and region, all Datomic cluster nodes have read permission on parameter store keys that begin with `datomic-shared`
- The ion library provides `get-params` as a convenience for reading parameter store parameters

#### Get-Params

The `get-params` function is a convenience abstraction over [GetParametersByPath](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_GetParametersByPath.html). `get-params` takes a map with a `:path` key, and it returns all the parameters under the path as a map from the parameter name string to parameter value string, decrypting if necessary.

For example, the call to `get-params` below returns all the parameters under the path `/datomic-shared/prod/deploy-monitor`.

```clojure
(ion/get-params {:path "/datomic-shared/prod/deploy-monitor"})
```

#### Per-System Permissions

The `datomic-shared` parameter prefix is readable by all Datomic systems in a particular AWS account and region. If you want more [granular permissions](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-access.html), you can choose your own naming convention (under a different prefix), and explicitly [add permissions to the IAM policy](../../05-operation/02-cloud/06-access-control/access-control.md#adding-an-iam-policy-to-datomic-nodes) for your Datomic nodes.

#### Configuration Example

The [deploy-monitor](https://github.com/Datomic/ion-event-example) sample application demonstrates using [app-info](#app-info-map), the [environment map](#environment-map), and [parameters](#parameters) together. First, the environment and app-info map are used to create a key prefix following the naming convention:

```text
/datomic-shared/(env)/(app-name)/
```

- `datomic-shared` is a prefix readable by all Datomic nodes
- `env` is taken from the environment map, and differentiates different environments for the same application, e.g. "ci" vs. "prod"
- `app-name` is the ion [app-name](#ion-config)

Then, this prefix is used to name configuration keys in the parameter store. Deploy-monitor needs four parameters:

- A Datomic database name
- A Slack channel
- Two encrypted tokens for Slack

Given this convention, the following AWS CLI commands will create the parameters for the "prod" environment:

```sh
# actual parameter values not shown
aws ssm put-parameter --name /datomic-shared/prod/deploy-monitor/db-name --value $DB_NAME --type String
aws ssm put-parameter --name /datomic-shared/prod/deploy-monitor/channel --value $CHANNEL --type String
aws ssm put-parameter --name /datomic-shared/prod/deploy-monitor/bot-token --value $BOT_TOKEN --type SecureString
aws ssm put-parameter --name /datomic-shared/prod/deploy-monitor/verification-token --value $VERIFICATION_TOKEN --type SecureString
```

You could use similar commands to create parameters for additional environments.

At runtime, deploy-monitor uses `get-app-info` and `get-env` to load the information needed to create a Parameter Store path, and then reads all of its parameter values with `get-params`:

```clojure
(def get-params
  "Returns the params under /datomic-shared/(env)/(app-name)/, where

env       value of get-env :env
app-name  value of get-app-info :app-name"
  (memoize
   #(let [app (or (get (ion/get-app-info) :app-name) (fail :app-name))
          env (or (get (ion/get-env) :env) (fail :env))]
      (ion/get-params {:path (str "/datomic-shared/" (name env) "/" app "/")})))))
```

## Naming Applications

When you [create a compute group](../../05-operation/02-cloud/05-compute-templates/compute-templates.md), you specify an AWS CodeDeploy application name to be used for deploying ions. By default, this name is the same as the name of your compute group.

Override the default and choose a common application name if you plan to have multiple compute groups run the same code, e.g. different development stages of the same application. For example, you might have two systems "inventory-staging" and "inventory-production", with the intent that code deployed to production has always been deployed and tested in staging first. You can give the compute groups in both systems a shared application name "inventory" when you create their compute stacks.

The application name cannot be changed after a compute group is created. If you need a different application name, simply [delete the compute group](../../05-operation/02-cloud/05-compute-templates/compute-templates.md) and [create a new one](../../05-operation/02-cloud/05-compute-templates/compute-templates.md#creating-a-compute-group).

## Best Practices

- Ions are designed to let you do most of your development and testing at a [Clojure REPL](https://clojure.org/guides/repl/introduction), with a much faster feedback loop than push/deploy. Take advantage of this wherever possible.
- You do not need ions, or a Datomic Cloud system, to get develop and test Datomic applications. You can start with [dev-local](../../01-setup/03-local-setup/local-setup.md), and when you are ready for ions, make your code portable with [divert-system](../../01-setup/03-local-setup/local-setup.md#using-datomic-local).
- Do **not** store AWS credentials in the Parameter Store, as Datomic Cloud [fully supports IAM roles](../../05-operation/02-cloud/06-access-control/access-control.md#authorize-ions-to-access-other-aws-services).
- The tools.deps classpath is the source of truth for an ion revision. If you need a file for local development that you do not want deployed, you should [add that file's directory with a tools.deps alias](https://clojure.org/guides/deps_and_cli#extra_paths). (You cannot e.g. "subtract" the file with a `.gitignore`. Ions use git only for calculating a SHA.)
- [Provision](../../09-tech-notes/08-lambda-provisioned-concurrency/lambda-provisioned-concurrency.md) AWS lambda concurrency to mitigate cold starts.
- If you have dependencies that are needed only in dev and test, place them [under a tools.deps alias](https://clojure.org/guides/deps_and_cli#extra_deps) so that you do not deploy them to production.

## Older Versions of Datomic Cloud

Datomic versions 781-9041 (2021-03-02) and older do not manage an [API Gateway](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#api-gateways) for ion applications. If you are running an older version of Datomic, the easiest way to deploy web services is to [upgrade Datomic](../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md) and follow the instructions above.

If this is not possible, the instructions below cover managing your own API Gateway. The instructions vary based on your [system topology](../../05-operation/02-cloud/11-how-to/how-to.md#check-system-topology-legacy).

For Datomic Solo, you can connect an API Gateway to a [web lambda proxy](#web-lambda-proxies). For Production, you can connect an API Gateway [to your Network Load Balancer](#api-gateway-to-network-load-balancer).

### Web Lambda Proxies

If you are running the Solo Topology, version 781-9041 and older, you can expose a web service using [API Gateway Lambda Proxy integration](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html).

To deploy a Lambda proxy web endpoint, you will need to:

- Develop and test an ordinary [web entry point](#http-direct-entry-point)
- Create a web lambda entry point with [ionize](#create-a-web-lambda-proxy-entry-point)
- [Configure an API Gateway](#configure-an-api-gateway-for-a-web-lambda-proxy)

#### Create a Web Lambda Proxy Entry Point

Given a var that implements a web entry point, you can create a Lambda proxy entry point by calling `ionize`. `ionize` takes a web entry point function and returns a function that implements the [special contract required by a lambda proxy](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html#api-gateway-simple-proxy-for-lambda-input-format).

The ion-starter sample project [has an example](https://github.com/Datomic/ion-starter/blob/295e69df0850dee9ff75b355c546b68e33207f95/src/datomic/ion/starter/http.clj#L30):

```clojure
(def get-items-by-type-lambda-proxy
  (apigw/ionize get-items-by-type))
```

You should then name this lambda proxy as a Lambda entry point (not an HTTP Direct entry point!) in your [ion-config](#ion-config).

Again, the ion-starter sample project [has an example](https://github.com/Datomic/ion-starter/blob/295e69df0850dee9ff75b355c546b68e33207f95/resources/datomic/ion-config.edn#L11-L12).

The input map for web lambda proxies includes the following additional keys:

| Key | Req'd | Value | Example |
|---|---|---|---|
| `:json` | No | [api-gateway/json](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html#api-gateway-simple-proxy-for-lambda-input-format) | {"resource":"{proxy+}","path":"datomic","httpMethod":"POST" … } |
| `:data` | No | [api-gateway/data](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html#api-gateway-simple-proxy-for-lambda-input-format) | {"Path": "/datomic","Querystringparameters": null, "Pathparameters": {"Proxy": "datomic"}…} |

#### Configure an API Gateway for a Web Lambda Proxy

After you [push](#push) and [deploy](#deploy) an ion application with a [web Lambda Proxy](#create-a-web-lambda-proxy-entry-point) entry point, you can create an API gateway as follows:

- Go to the [AWS API gateway console](https://console.aws.amazon.com/apigateway/home) to create a new AWS API gateway.
  - If this is the first AWS API Gateway you are creating, choose "Get started."
  - Then click "Ok" and choose the "New API" radio button.
  - If you have existing AWS API Gateways, choose "Create API."
- Give your API a unique name, e.g. "ion-get-items-by-type". Leave the other default settings.
- Click "Create API" in the bottom right.
- Under the "Actions" dropdown, choose "Create resource."
- Check the box "Configure as proxy resource."
  - Other fields will be updated with defaults. Leave these as is.
- If you will need to support CORS, then select the "Enable API Gateway CORS" option. This will set up an `OPTIONS` method with a Mock Integration Request which can be changed to fit your needs.
- Click "Create resource."
- Set the lambda function to the [name of your Lambda proxy](#invoking-lambdas) and click "Save."
  - Note that the autocomplete does not always work correctly on this step, so trust and verify your own spelling.
- Choose "Ok" to give API Gateway permission to call your lambda.
- Under your API on the left side of the UI, click on the bottom choice "Settings."
- Choose "Add binary media type", and add the `*/*` type, then "Save changes."

You can then deploy your API gateway on the public internet:

- Select your API name (if you have not already), and choose "Deploy API" under the "Actions" dropdown.
- Choose "New stage" as the deployment stage.
- Choose a "Stage name", then choose "Deploy."

The top of the Stage Editor will show the Invoke URL for your deployed app. Your service will be available at the path `https://$(Invoke URL)/datomic`.

### API Gateway to Network Load Balancer

If you are running the Production Topology, version 781-9041 and older, you can expose a web service by connecting an API Gateway to a compute group's Network Load Balancer as follows:

- Develop and test an ordinary [web entry point](#http-direct-entry-point)
- [Create a VPC Link](#create-a-vpc-link)
- [Configure an API Gateway](#create-an-api-gateway)

#### Create a VPC Link

The list of all VPC Links active in your system can be found in the [API gateway console](https://console.aws.amazon.com/apigateway/home#/vpc-links).

- Create a [VPC Link](https://console.aws.amazon.com/cloudformation/home#/stacks) in the AWS console.
- In the "Target NLB" dialog, choose the NLB for your Datomic Cloud system.
  - The NLB will be selectable from a dropdown.
  - The NLB name can be found in the Outputs tab of your compute or query group [CloudFormation stack entry](https://console.aws.amazon.com/cloudformation/home#/stacks) under the `LoadBalancerName` key.
- The newly created VPC Link will show `Status: Pending` for some time. Wait until the status changes to `Available` before proceeding.
- Create an API Gateway that uses the VPC link.

#### Create an API Gateway

To create an API Gateway associated with a specific VPC link:

- Ensure that the [VPC Link](https://console.aws.amazon.com/apigateway/home#/vpc-links) that you [created](#create-a-vpc-link) in the previous step shows "Status: Available."
- Go to the [AWS API Gateway console](https://console.aws.amazon.com/apigateway/home).
  - If this is the first AWS API Gateway you are creating, choose **Get Started**.
  - Then click "Ok" and choose the "New API" radio button.
  - If you have existing AWS API Gateways, choose "Create API."
- Give your API a unique name, leave the other default settings.
- Click "Create API" in the bottom right.
- Under the "Actions" dropdown, choose "Create resource."
- Check the box "Configure as proxy resource".
  - Other fields will be updated with defaults. Leave these as is.
- Click "Create resource."
- Select **VPC Link** as the "Integration type."
- Check the box "Use proxy integration."
- Select the desired VPC Link target for this API gateway from the dropdown box.
- Enter your `http://$(NLB URI):port/{proxy}` as the "Endpoint URL." This NLB URI can be found in the outputs tab of your compute or query group [CloudFormation template entry](https://console.aws.amazon.com/cloudformation/home#/stacks) under the `LoadBalancerHttpDirectEndpoint` key:

![ions-api-gw-proxy.png](https://docs.datomic.com/images/ions-api-gw-proxy.png)

- Click "Save."

You can then deploy your API gateway on the public internet:

- Select your API name (if you have not already), and choose "Deploy API" under the "Actions" dropdown.
- Choose "New stage" as the deployment stage.
- Choose a stage name, then choose "Deploy."

The top of the Stage Editor will show the Invoke URL for your deployed app. Your service will be available at the path `https://$(Invoke URL)/datomic`.

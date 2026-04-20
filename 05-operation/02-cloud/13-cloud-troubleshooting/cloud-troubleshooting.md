# Troubleshooting

- [Troubleshooting CloudFormation templates](#troubleshooting-cloudformation-templates)
- [Troubleshooting compute nodes](#troubleshooting-compute-nodes)
- [Troubleshooting solo stacks](#troubleshooting-solo-nodes)
- [Troubleshooting clients](#troubleshooting-client-errors)
- [Troubleshooting socks proxy](#troubleshooting-socks-proxy-errors)
- [Troubleshooting ions](#troubleshooting-ions)

Datomic Cloud is configured with a set of well-tested default parameters for scaling and provisioning. Do **not** alter system settings (storage provisioning and scaling, changes to network or instance configurations, alterations to the default CloudFormation templates) without first contacting [Datomic Support](https://www.datomic.com/support.html) unless the changes are explicitly described in the Datomic documentation.

## Troubleshooting CloudFormation Templates

### Failures in Nested Templates

If a nested template fails, the root template will fail also, and the console will by default show only the root template's `CREATE_FAILED` event. To see the important details in the nested template's events:

- Click on the filter popup, and choose "Deleted"

  [![template-filters.png](https://docs.datomic.com/images/template-filters.png)](https://docs.datomic.com/images/template-filters.png)

- Select the nested template, and then choose the `Events` tab to see the cause of failure

### Check for CloudFormation Failure

To check your CloudFormation for errors, find your stack in the [CloudFormation](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active&tab=events) window and click the checkbox at the start of its row. Note that:

- If the status of the stack is `CREATE_IN_PROGRESS` the stack is still being created, and you should continue to wait for the stack to start. You can monitor it from the Cloudformation details window. The stack is done when it says "CREATE_COMPLETE".
- If the status of the stack is `CREATE_FAILED` there was an error starting the stack. See the other topics on this page for help tracking down the source of the failure.

### Production Topology Create Failed

If the production topology fails to create in CloudFormation with a *CREATE_FAILED* on the `AWS::AutoScaling::AutoScalingGroup` for the `TxAutoScalingGroup` resource with the message:

```
You have requested more instances (2) than your current instance limit of 1 allows for the specified instance type. Please visit http://aws.amazon.com/contact-us/ec2-request to request an adjustment to this limit. Launching EC2 instance failed.
```

You need to [request a limit increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) in your allowed number of running i3.large instances.

### Starting a Compute Stack without a Storage Stack

Without a storage system, compute will fail immediately and roll back. The stack event message will report the following error, substitute `<system-name>` for the system name you specified when launching your compute stack:

```
No export named <system-name>-FileSystemId found. Rollback requested by user.
```

[![compute-only-error.png](https://docs.datomic.com/images/compute-only-error.png)](https://docs.datomic.com/images/compute-only-error.png)

### Running in EC2 Classic

Datomic Cloud is currently only supported in accounts that support only EC2-VPC. If you attempt to run Datomic Cloud in an EC2-Classic, the Cloudformation stack will fail. The stack event message will have an error with a type of `Custom::ResourceCheck` and a logical ID of `EnsureEc2Vpc`.

See [setting up](../../../01-setup/02-cloud-setup/02-cloud-setup/cloud-setup.md) for more information.

### Update Failed with "Export Cannot Be Deleted"

A failed compute stack update to or past version 470-8654 with the error:

```
Export <SystemName>-VpcEndpointServiceName cannot be deleted as it is in use by <systemname>-vpc-endpoint
```

Indicates that you have previously launched a VPC Endpoint using the provided Endpoint CloudFormation template. Versions from 470-8654 on do not create the necessary services for the VPC Endpoint template. Delete the VPC Endpoint stack from the [CloudFormation dashboard](https://console.aws.amazon.com/cloudformation/home) and [recreate the endpoint](../09-vpc-access/vpc-access.md#creating-vpc-service-and-endpoint).

## Troubleshooting Compute Nodes

### Check Alerts First

Datomic publishes a [metric](../12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud) named `Alert` for any situation that might require operator intervention. You can create a Cloudwatch Alarm for the `Alert` metric, and then [search the logs](../12-monitoring-cloud/monitoring-cloud.md#finding-alerts) for specific details.

Reviewing alerts should be your first step when you are troubleshooting Datomic Cloud nodes.

### Check Memory Second

Running out of memory is the number one cause of both availability and performance problems.

Every Datomic node publishes a JvmFreeMb [metric](../12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud). If the Minimum value for this metric dips below 15% of its initial startup value for an extended period, memory pressure is likely a problem. Consider the following:

- If you believe the problem is *individual large queries*, upgrade to larger EC2 instance sizes
- If you believe the problem is a *high volume of smaller queries*, increase the number of instances in your compute group

## Troubleshooting Solo Nodes

### Check Alerts and Logs First

As described [above](#check-alerts-first), Datomic publishes a [metric](../12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud) named `Alert` for any situation that might require operator intervention. You can create a Cloudwatch Alarm for the `Alert` metric, and then [search the logs](../12-monitoring-cloud/monitoring-cloud.md#finding-alerts) for specific details.

When troubleshooting a Solo system, ensure that the single compute node in your system is healthy and reporting [logs](../12-monitoring-cloud/monitoring-cloud.md#logs-produced-by-datomic-cloud) and [metrics](../12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud). If it is not, you should use the [EC2 dashboard](https://console.aws.amazon.com/ec2/) to terminate your compute node and allow the AutoScaling group to replace it.

If you require an HA system with built-in monitoring and failover, you should move to the [production topology](../01-cloud-architecture/cloud-architecture.md#primary-compute-stack).

## Troubleshooting Client Errors

### Missing Connect Arguments

If you leave out a required argument, the following message will be displayed:

```
CompilerException clojure.lang.ExceptionInfo: Expected string for :query-group {:cognitect.anomalies/category :cognitect.anomalies/incorrect, :cognitect.anomalies/message "Expected string for :query-group", :datomic.client.impl.shared.validator/got {:server-type :cloud, :region "us-east-1", :system "errortest2", :endpoint "http://entry.errortest2.us-east-1.datomic.net:8182/", :proxy-port 8888}, :datomic.client.impl.shared.validator/op :client, :datomic.client.impl.shared.validator/requirements {:region string, :system string, :query-group string, :endpoint string, :server-type keyword}}, compiling:(form-init8129817634595122502.clj:1:13)
```

### ::cognitect.anomalies/forbidden

If you get a *forbidden* error when using the Client API, this means that your AWS credentials do not grant permission for this functionality. To solve it:

- Make sure that you have an IAM policy that [authorizes](../06-access-control/access-control.md) the operation you are trying to perform
- Make sure that policy is associated with the identity you are running under

### ExceptionInfo Forbidden to Read Keyfile

If the following message appears, ensure that you have sourced the right credentials with all necessary permissions:

```
>d/create-database client {:db-name "test"})
ExceptionInfo Forbidden to read keyfile at s3://datomic-test-storagef7f305e7-1bpzuyaf5d-s3datomic-1wgl6uvtl9bei/datomic-test/datomic/access/admin/.keys. Make sure that your endpoint is correct, and that your ambient AWS credentials allow you to GetObject on the keyfile.  clojure.core/ex-info (core.clj:4739)
```

### Connection Failure

The client error:

```clojure
Exception in thread "main" clojure.lang.ExceptionInfo: Unable to connect to system:
{:cognitect.anomalies/category :cognitect.anomalies/fault, :cognitect.anomalies/message "SOCKS4 tunnel failed, connection closed", :cognitect.http-client/throwable #error {
 :cause "SOCKS4 tunnel failed, connection closed"
 :via
  [{:type java.io.IOException
    :message "SOCKS4 tunnel failed, connection closed"
    :at [org.eclipse.jetty.client.Socks4Proxy$Socks4ProxyConnection onFillable "Socks4Proxy.java" 166]}] ...
```

Accompanied by the following message from your SOCKS Proxy:

```
debug1: channel 2: new [dynamic-tcpip]
channel 2: open failed: administratively prohibited: open failed
```

Indicates that the client was unable to reach the Datomic system through the proxy. Check your configuration `:endpoint` carefully and use the [access gateway connection test](../17-access-gateway-legacy/access-gateway-legacy.md#test-access-gateway-connection) to ensure your proxy is configured correctly.

### java.lang.NoClassDefFoundError: javax/xml/bind/DatatypeConverter

> This issue is resolved on [client-cloud 0.8.63](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#20180821-0863-client-cloud-update)

On `com.datomic/client-cloud 0.8.56` and Java 9+ you'll run into this error when connecting:

```
CompilerException clojure.lang.ExceptionInfo: java.lang.NoClassDefFoundError: javax/xml/bind/DatatypeConverter #:cognitect.anomalies{:category :cognitect.anomalies/fault, :message "java.lang.NoClassDefFoundError: javax/xml/bind/DatatypeConverter"}, compiling:(form-init692047429040487558.clj:1:11)
```

As of Java 9 the `jaxb` module was removed from the default JDK. As such, you must explicitly require the `javax.xml.bind/jaxb-api {:mvn/version "2.3.0"}` dep to resolve this error.

### Jetty Dependency Conflict

The following error occurs if your project has a transient dependency on a more recent version of Apache Jetty than the version used by Datomic:

```clojure
#error {
 :cause "org.eclipse.jetty.util.thread.Invocable$InvocationType"
  :via
   [{:type java.lang.NoClassDefFoundError
     :message "org/eclipse/jetty/util/thread/Invocable$InvocationType"
     :at [org.eclipse.jetty.io.ManagedSelector <init> "ManagedSelector.java" 79]}
    {:type java.lang.ClassNotFoundException
     :message "org.eclipse.jetty.util.thread.Invocable$InvocationType"
     :at [java.net.URLClassLoader findClass "URLClassLoader.java" 381]}]
 ...}
```

The conflict can be resolved by excluding the version of Jetty used by Datomic:

```clojure
[com.datomic/client-cloud "0.8.54"
 :exclusions [org.eclipse.jetty/jetty-client
              org.eclipse.jetty/jetty-http
              org.eclipse.jetty/jetty-util]]
```

### Loading Database Error When Connecting

The "Loading database" message:

```clojure
#:cognitect.anomalies{:category :cognitect.anomalies/unavailable,
                      :message "Loading database"}
```

indicates that the Datomic node you've reached has not yet loaded the requested database into memory. This error can occur when you call `create-database`, followed immediately by `connect`, as well as following an instance restart. You should consider this a retry-able error and wrap the connection attempt with retry logic.

### ::cognitect.anomalies/busy

If you get a *busy* response from the Client API, your request rate has temporarily exceeded the capacity of a node, and has already been through an exponential backoff and retry implemented by the client. At this point you have three options:

- When transacting continue to retry the request at the application level with you own exponential backoff. The [Mbrainz importer](https://github.com/Datomic/mbrainz-importer) example project demonstrates a [batch import with retry](https://github.com/Datomic/mbrainz-importer/blob/master/src/cognitect/xform/batch.clj#L70-L92).
- When querying, expand the capacity of your system, by [adding and scaling a query group](../03-growing-your-system/growing-your-system.md#adding-and-scaling-a-query-group).
- Give up on completing the request.

### Server Type Mismatch in Datomic Client

Ions support the *synchronous* Datomic Client API. The error:

```json
{
    "Msg": "IonLambdaException",
    "Ex": {
        "Cause": ":server-type must be :cloud or :peer-server",
        "Via": [
            {
                "Type": "clojure.lang.ExceptionInfo",
                "Message": ":server-type must be :cloud or :peer-server",
                "Data": {
                    "CognitectAnomaliesCategory": "CognitectAnomaliesIncorrect",
                    "CognitectAnomaliesMessage": ":server-type must be :cloud or :peer-server"
                },
                "At": [
                    "clojure.core$ex_info",
                    "invokeStatic",
                    "core.clj",
                    4739
                ]
            }
```

Indicates an attempt to use the async API with the server-type `:ion`.

You should use the synchronous Client API in your Ion(s).

## Troubleshooting Socks Proxy Errors

### Unsupported AWS CLI Version

> Information for previous CLI tools: please update to [the latest version](../../../11-releases/releases.md).

This error is indicated when the script prints a generic AWS CLI help message before failing:

```sh
$ bash datomic-access client -r eu-central-1 good-system
```

```sh
To see help text, you can run:

  aws help
  aws <command> help
  aws <command> <subcommand> help
aws: error: argument command: Invalid choice, valid choices are:
...
```

To fix it, update to the latest version of the [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html).

### Wrong AWS Creds

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#20210302-781-9041-compute-update) and lower.

> Information for previous CLI tools: please update to [the latest version](../../../11-releases/releases.md).

```sh
$ bash datomic-access client -r eu-central-1 good-system
```

```sh
Unable to read bastion key, make sure your AWS creds are correct.
```

To fix it, ensure your AWS creds are connected to a policy that is [authorized](../06-access-control/access-control.md) for your Datomic system.

### Wrong System Name

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#20210302-781-9041-compute-update) and lower.

> Information for previous CLI tools: please update to [the latest version](../../../11-releases/releases.md).

```sh
$ bash datomic-access client -r eu-central-1 not-a-system
```

```sh
Datomic system not-a-system not found, make sure your system name and AWS creds are correct.
```

### AccessDeniedException

The error below indicates that the configured AWS credentials do not match what is necessary to access the indicated system. You may be using the wrong credentials for the account:

```
An error occurred (AccessDeniedException) when calling the GetResources operation: User: arn:aws:iam::<number>:user/<user-name> is not authorized to perform: tag:GetResources
Datomic system <system-name> not found, make sure your system name and AWS creds are correct.
```

To fix it, ensure you [configured the correct AWS credentials.](../../../01-setup/02-cloud-setup/01-aws-account-setup/aws-account-setup.md)

### SOCKS4 Tunnel Failed, Connection Closed and Connection Refused Errors in Client

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#20210302-781-9041-compute-update) and lower.

> Information for previous CLI tools: please update to [the latest version](../../../11-releases/releases.md).

This error will throw when the connection is unable to find a Datomic Socks proxy, if you're seeing this error while running remotely, it is because the Client is expecting a proxy and you'll need to remove the proxy from your config map:

```clojure
CompilerException clojure.lang.ExceptionInfo: Unable to connect to localhost:8182 {:cognitect.anomalies/category :cognitect.anomalies/fault, :cognitect.anomalies/message "SOCKS4 tunnel failed, connection closed", :cognitect.http-client/throwable #error {
 :cause "SOCKS4 tunnel failed, connection closed"
 :via
   [{:type java.io.IOException
 :message "SOCKS4 tunnel failed, connection closed"
 :at [org.eclipse.jetty.client.Socks4Proxy$Socks4ProxyConnection onFillable "Socks4Proxy.java" 166]}]
 :trace
   [[org.eclipse.jetty.client.Socks4Proxy$Socks4ProxyConnection onFillable "Socks4Proxy.java" 166]
   [org.eclipse.jetty.io.AbstractConnection$ReadCallback succeeded "AbstractConnection.java" 273]
   [org.eclipse.jetty.io.FillInterest fillable "FillInterest.java" 95]
   [org.eclipse.jetty.io.SelectChannelEndPoint$2 run "SelectChannelEndPoint.java" 75]
   [org.eclipse.jetty.util.thread.strategy.ExecuteProduceConsume produceAndRun "ExecuteProduceConsume.java" 213]
   [org.eclipse.jetty.util.thread.strategy.ExecuteProduceConsume run "ExecuteProduceConsume.java" 147]
   [org.eclipse.jetty.util.thread.QueuedThreadPool runJob "QueuedThreadPool.java" 654]
   [org.eclipse.jetty.util.thread.QueuedThreadPool$3 run "QueuedThreadPool.java" 572]
   [java.lang.Thread run "Thread.java" 745]]}, :config {:server-type :cloud, :region "us-east-1", :system "wrong", :query-group "wrong", :endpoint "http://entry.wrong.us-east-1.datomic.net:8182/", :proxy-port 8182, :endpoint-map {:headers {"host" "entry.wrong.us-east-1.datomic.net:8182"}, :scheme "http", :server-name "entry.wrong.us-east-1.datomic.net", :server-port 8182}}}, compiling:(sync_client_test.clj:138:13)
```

It throws when the config map is pointing to the wrong proxy port.

The error below is displayed when using the `datomic-access` if the connection or proxy process has failed:

```clojure
CompilerException clojure.lang.ExceptionInfo: Unable to connect to localhost:8188 {:cognitect.anomalies/category :cognitect.anomalies/unavailable, :cognitect.anomalies/message "Connection refused", :config {:server-type :cloud, :region "us-east-1", :system "jaret-lambda-test", :query-group "jaret-lambda-test", :endpoint "http://entry.jaret-lambda-test.us-east-1.datomic.net:8182/", :proxy-port 8188, :endpoint-map {:headers {"host" "entry.jaret-lambda-test.us-east-1.datomic.net:8182"}, :scheme "http", :server-name "entry.jaret-lambda-test.us-east-1.datomic.net", :server-port 8182}}}, compiling:(form-init7068544986673255826.clj:1:13)
```

You will see this error when using the `datomic-access` if the connection or proxy process has failed.

### Error Retrieving Results

The `datomic` command is unable to access the Datomic system. Specify an [AWS credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) source using `-p` **or** `--profile`, and make sure the currently specified credentials are correct for the Datomic system that you are trying to access.

## Troubleshooting Ion push

### Ion push requires a clean git commit

If you attempt to [*push*](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#push) an ion project with uncommitted local changes, you will see the following exception:

```
java.lang.IllegalArgumentException: You must either specify a uname or deploy from clean git commit
```

You must either:

- Push from a fully clean git commit (recommended)
- Provide an [unreproducible name](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#unreproducible-push)

### Ion Push Fails to Find a Code Bucket

The ion push operation pushes your code to a region-specific Datomic code bucket. If push fails to find a code bucket, it will report a message like:

```clojure
{:command-failed "{:op :push}",
 :causes
 ({:message
   "Unable to find a unique code bucket. Make sure that you are running\nDatomic Cloud in the region you are deploying to.",
   :class ExceptionInfo,
   :data {"datomic:code" nil}})}
```

The most common cause of this is that you are accidentally using the wrong AWS keys, or your AWS credentials configuration is defaulting to the wrong AWS region. Ensure you are using the right AWS access keys.

### Ion Push Requires Ion-Dev Tools

The ion-dev tools are invoked via a deps.edn alias. If you have not installed the ion-dev tools, the Clojure CLI will report that the alias is undeclared when you try to push:

```sh
clojure -A:ion-dev '{:op :push}'
```

```clojure
WARNING: Specified aliases are undeclared: [:ion-dev]
Execution error (FileNotFoundException) at java.io.FileInputStream/open0 (FileInputStream.java:-2).
{:op :push} (No such file or directory)
```

If you encounter this error, [install the ion-dev tools](../11-how-to/how-to.md#install-ion-dev-tools).

### Ion Push Requires a Recent Version of Clojure CLI

The ion-dev tools require a recent version of the Clojure CLI. If the following error is displayed, then you need a [more recent version](../11-how-to/how-to.md#install-clojure-cli) of the Clojure CLI.

```
Error building classpath. Unknown alias key: :deps
```

## Troubleshooting Ion deploy

### Ion deploy fails with "Assert failed" message

Failure of [ion deployment](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#deploy) with the following error message in the [Datomic system CloudWatch logs](../12-monitoring-cloud/monitoring-cloud.md#searching-cloudwatch-logs) indicates that a namespace in the ion attempted to create a connection to a Datomic system as a side effect of loading a namespace:

```json
"Cause": "Assert failed: cfg",
"Via": [
    {
        "Type": "java.lang.AssertionError",
        "Message": "Assert failed: cfg",
        "At": [
            "datomic.client.impl.local$create_client",
            "invokeStatic",
            "local.clj",
            97
        ]
    }
]
```

You should follow the "connect on use" pattern as shown in the [ion starter](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter.clj#L13) example.

## Troubleshooting Ion Deploy-Status

If your ion-deploy succeeds but never reaches `SUCCEEDED` status, likely your application code cannot load correctly on your compute nodes. Follow the steps for [troubleshooting compute nodes](#troubleshooting-compute-nodes).

## Troubleshooting Ions

### Supported Lambda Ion Return Types

Returning an unsupported type from a Lambda Ion will result in the exception:

```
datomic.ion.lambda.handler.exceptions.Incorrect: No implementation of method: :->bbuf of protocol: #'datomic.ion.lambda.dispatcher/ToBbuf found for class: clojure.lang.LazySeq
```

Lambda ions [should return](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#lambda-entry-point) a String, InputStream, ByteBuffer, or File.

### API Gateway Returns a 500 Internal Server Error

A `HTTP 500 Internal server error` returned from API Gateway indicates that the ion code threw an exception. Find the full exception text and stack trace in the Datomic system CloudWatch logs:

- Open the [CloudWatchlLogs](https://console.aws.amazon.com/cloudwatch/home#logs:) in the AWS console
- Click on the Log Group named "datomic-{System}", where System is your system name. Each EC2 instance will create a separate log stream. Usually, you will want to search across all log streams.
- Click the "Search Log Group" button.
- Enter "Exception" in the search dialog.
- Find the event of interest in the search results and click the arrow to expand the details.

The screenshot below shows finding an ion exception in the log:

[![ion-exception-log.png](https://docs.datomic.com/images/ion-exception-log.png)](https://docs.datomic.com/images/ion-exception-log.png)

## Troubleshooting OPTIONS Requests Blocked by CORS Policy

If you are leveraging authorization, such as Cognito, in your API Gateway on your ANY HTTP method, then you will receive an error when your browser makes the preflight OPTIONS request. The error will manifest as a failed request and a message similar to:

**On Chrome**:

```
Access to XMLHttpRequest at 'https://a3gy67dew7.execute-api.us-east-1.amazonaws.com/dev/api/v1/authenticated-example' from origin 'http://localhost:9876' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

**On Firefox**:

```
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at https://a3gy67dew7.execute-api.us-east-1.amazonaws.com/dev/api/v1/authenticated-example. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

To fix it follow the instructions found in [enabling CORS in a lambda proxy web service](../../../09-tech-notes/10-enabling-cors-in-lambda-proxy/enabling-cors-in-lambda-proxy.md).

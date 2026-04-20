# Datomic Cloud Change Log

## 2026/02/17 - 1217-9399

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1217-9399` | `1217-9399` | `1217-9399` |

- Performance: Improve indexing performance, reduce I/O in large databases
- Fix: Indexing slowly leaks garbage segments in storage which are unrecoverable by datomic garbage collection
- Upgrade: The AMI is now based on the [2026/01/20 version of the Linux 2023 base AMI](https://docs.aws.amazon.com/linux/al2023/release-notes/relnotes-2023.10.20260120.html).

## 2026/02/17 - 1.0.352 - Ion-Dev

| Ion dev |
|---|
| `1.0.352` |

- Upgrade: org.clojure/tools.deps.alpha to org.clojure/tools.deps 0.24.1523
- Updated dep list for Datomic Cloud 1217-9399

## 2025/08/12 - 1172-9392

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1172-9392` | `1172-9392` | `1172-9392` |

- Fix: Prevent a problem where in rare situations indexing jobs can fail to progress indefinitely.

## 2025/06/17 - 1.0.326 - Ion-Dev

| Ion dev |
|---|
| `1.0.326` |

- Updated dep list for Datomic Cloud 1171-9390

## 2025/06/17 - 1171-9390

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1171-9390` | `1171-9390` | `1171-9390` |

- Performance: Reduce memory usage in large databases
- Performance: Improve indexing performance, reduce I/O in large databases
- Upgraded org.clojure/core.async to 1.8.741
- Upgrade: CFT Lambda Runtime to nodejs22.x. See: https://aws.amazon.com/blogs/compute/node-js-22-runtime-now-available-in-aws-lambda/

## 2025/04/16 - 1162-9380

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1162-9380` | `1162-9380` | `1162-9380` |

- Performance: Reduce memory required to calculate db-stats.
- Performance: Reduce memory required to calculate index-metrics.
- Performance: Improved operation throughput on primary compute instances larger than `t3.small`.
- Performance: Reduce memory required for indexing.

## 2025/02/03 - 1.0.131 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.131` |

- Updated client-cloud to 1.0.139 to address reflection warnings introduced with JDK19

## 2024/08/23 - 1126-9340

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1126-9340` | `1126-9340` | `1126-9340` |

- Upgraded Clojure to 1.11.4. See https://clojure.org/news/2024/08/03/clojure-1-11-4

## 2024/07/11 - 1125-9339

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1125-9339` | `1125-9339` | `1125-9339` |

- Feature: tx-stats. See https://docs.datomic.com/reference/tx-stats.html
- Performance: Improve performance in transactions and calls to d/with by prefetching reads. Config option `datomic.prefetchConcurrency` limits the number of concurrent prefetches. See https://docs.datomic.com/operation/system-properties.html
- Fix: Ensure datoms are virtual and are no longer added to the log.
- Upgraded com.datomic/client-cloud to 1.0.130
- Upgraded com.datomic/ion to 1.0.71

## 2024/07/11 - 1.0.130 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.130` |

- Upgraded com.cognitect/s3-creds to 1.0.31

## 2024/07/11 - 1.0.71 - Ion

| Ion |
|---|
| `1.0.71` |

- Upgraded com.cognitect/transit-clj to 1.0.333

## 2024/07/11 - 1.0.325 - Ion-Dev

| Ion dev |
|---|
| `1.0.325` |

- Updated dep list for Datomic Cloud 1125-9339

## 2024/02/12 - 1.0.125 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.125` |

- Fix: correctly deserialize URIs to java.net.URI

## 2023/12/20 - 1102-9309

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1102-9309` | `1102-9309` | `1102-9309` |

- Enhancement: migrate CloudFormation templates to use AutoScalingGroup LaunchTemplate instead of the deprecated LaunchConfiguration.
- Fix: pull expression could return true when supplying a `:default` for a boolean attribute.
- Fix: regression introduced in 990-9202 that could cause a database with blank keyword idents to fail to load.
- Performance: replace BufferedInputStream with an unsynchronized stream.
- Upgrade CFT Lambda Runtime to nodejs18.x.
- Upgraded core.async to 1.6.681.
- Upgraded commons-codec to 1.16.0.
- Upgraded aws-java-sdk-bundle to 1.12.564.
- Upgraded memcache-asg-java-client to 1.1.0.36.
- Upgraded http-client to 1.0.126.
- Upgraded transit-clj to 1.0.333.
- Upgraded client-cloud to 1.0.124.
- Upgraded fressian to 0.6.8.
- Upgraded guava to 32.0.1-jre.
- Upgraded jul-to-slf4j to 1.7.36.
- Upgraded log4j-over-slf4j to 1.7.36.
- Upgraded jcl-over-slf4j to 1.7.36.
- Upgraded tools.namespace to 1.4.4.
- Downgrade: The AMI is now based on the 2023/10/16 version of the Linux 2023 base AMI.

## 2023/12/20 - 1.0.124 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.124` |

- Upgraded core.async to 1.6.681.
- Upgraded commons-codec to 1.16.0.
- Upgraded aws-java-sdk-bundle to 1.12.564.
- Upgraded http-client to 1.0.126.
- Upgraded org.eclipse.jetty/jetty-* to 9.4.52.v20230823.
- Upgraded org.msgpack/msgpack to 0.6.12.

## 2023/12/20 - 1.0.68 - Ion

| Ion |
|---|
| `1.0.68` |

- Upgraded aws-java-sdk-ssm to 1.12.564.

## 2023/12/20 - 1.0.316 - Ion-Dev

| Ion dev |
|---|
| `1.0.316` |

- Upgraded Clojure to 1.11.1.
- Upgraded AWS Java SDK to 1.12.564.
- Upgraded org.yaml/snakeyaml to 2.2.

## 2023/10/09 - 1067-9276

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `1067-9276` | `1067-9276` | `1067-9276` |

- Performance: reduced resources required by indexing.
- Performance: improved EFS IOPS usage.
- Performance: indexing is more aggressive in removing `:db/noHistory` datoms.
- Performance: improved fressian read.
- Upgrade: NodeJS used in Lambdas upgraded to 16.x.
- Upgrade: Java on compute nodes upgraded to Java 17.
- Upgrade: The AMI is now based on the [2023/08/09 version of the Linux 2023 base AMI](https://docs.aws.amazon.com/linux/al2023/release-notes/relnotes-2023.1.20230809.html).
- Enhancement: use latest [AWS recommendations](https://docs.aws.amazon.com/efs/latest/ug/mounting-fs-nfs-mount-settings.html) for mounting EFS Volumes.
- Enhancement: support all instance types in the i3 family.
- Fix: prevent a problem where in rare situations a node could stop applying transactions.

## 2023/06/16 - 995-9204

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `995-9204` | `995-9204` | `995-9204` |

Datomic cloud AMIs are free of any markup outside the AWS marketplace under the [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0.html). Check the [Datomic Cloud is Free blog post](https://blog.datomic.com/2023/06/datomic-cloud-is-free.html).

## 2023/02/28 - 990-9202

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `990-9202` | `990-9202` | `990-9202` |

- Performance: Datomic now uses caffeine instead of guava for the object cache.
- Performance: improved fressian read.
- Feature: io-stats for transactions and queries. See [Io-stats](../../../04-apis/10-io-stats/io-stats.md).
- Feature: query-stats. See [Query-stats](../../../04-apis/11-query-stats/query-stats.md).
- Fix: attribute names that begin with an underscore can be used for forward lookups in entity and pull. Note that naming attributes with a leading underscore is strongly discouraged because it conflicts with the convention for reverse lookup.
- Fix: critical bug preventing transactions in 9188.
- Fix: pull performance regression introduced in 9188.
- Fix: exception when using io-stats in nested contexts, you should no longer see "No implementation of method: :-merge-stats of protocol".
- Upgraded AWS libraries to 1.12.358.

## 2023/02/28 - 1.0.123 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.123` |

- Feature: query-stats for transactions and queries. Check [Query-stats](../../../04-apis/11-query-stats/query-stats.md).

## 2022/11/29 - 981-9188

> The 981-9188 release templates have been removed (12/12/2022) per [Critical Notice](../../releases.md#critical-notices).
>
> This release introduced a problem in Datomic that could eventually halt transactions (but not Reads). All templates have been removed and de-listed from the docs and marketplace. The 990-9202 release contains all features in this release and replaces 981-9188.
>
> Recommendation: if you upgraded to this specific version it is to immediately upgrade again to the latest 990-9202.

## 2022/11/29 - 1.0.62 - Ion

| Ion |
|---|
| `1.0.62` |

- Fix: requiring `dev-local` no longer disables `ion.cast/initialize-redirect`.

## 2022/11/29 - 1.0.122 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.122` |

- Feature: Io-stats for transactions and queries. Check [io-stats](../../../04-apis/10-io-stats/io-stats.md).

## 2022/06/01 - 973-9132

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `973-9132` | `973-9132` | `973-9132` |

- Upgrade Clojure to 1.11.1.
- Upgrade AWS SDK to 1.12.132.
- Upgrade com.cognitect/s3-creds to 1.0.27.
- Upgrade com.datomic/memcached-asg-java-client to 1.1.0.33.
- Upgrade transit-clj to 1.0.329.
- Upgrade com.datomic/client to 1.0.126.
- Upgrade com.datomic/client-cloud to 1.0.120.
- Upgrade tools.namespace to 1.2.0.
- Upgrade The AMI is now based on the 3/16/2022 version of Linux 2 base AMI.
- Upgrade NodeJS used in Lambdas to 14.x.
- Add new output `IonApiIntegrationId`.
- Upgrade Corretto11 to 11.0.14+10.

## 2022/04/06 - 1.0.59 - Ion

| Ion |
|---|
| `1.0.59` |

- Upgrade transit-clj to 1.0.329.
- Upgrade aws-java-sdk-ssm to 1.12.132.

## 2022/04/06 - 1.0.306 - Ion-Dev

| Ion dev |
|---|
| `1.0.306` |

- Upgrade aws-java-sdk-* to 1.12.132.
- Upgrade s3-libs to 1.0.46.

## 2021/04/06 - 1.0.120 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.120` |

- Upgrade Client to 1.0.126.
- Upgrade s3-creds to 1.0.27.
- Upgrade aws-java-sdk-s3 to 1.12.132.

## 2022/04/01 - 1.0.304 - Ion-Dev

- Resolved [an issue](https://clojure.org/news/2022/04/01/deref#_from_the_core) where a user may encounter the warning:

```text
"WARNING: abs already refers to: #'clojure.core/abs in namespace: cognitect.s3-libs.file, being replaced by: #'cognitect.s3-libs.file/abs"
```

## 2022/01/14 - 939-9127

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `939-9127` | `939-9127` | `939-9127` |

- Upgraded AWS Java SDK to 1.12.100.
- Upgraded Jetty to 9.4.41.v20210927.
- Upgraded core.async to 1.5.6348.
- Upgraded core.cache to 1.0.225.
- Upgraded core.memoize to 1.0.253.
- Upgraded data.priority-map to 1.1.0.
- Upgraded tools.analyzer to 1.1.0.
- Upgraded tools.analyzer.jvm to 1.2.2.
- Upgraded tools.namespace to 1.1.1.
- Upgraded tools.reader to 1.3.6.
- Upgraded asm to 9.2.
- Upgraded org.slf4j to 1.7.32.
- Upgraded http-client to 1.0.111.
- Upgraded Guava to 31.0.1.
- Upgraded commons-codec to 1.15.
- Upgraded Java 11 to 11.0.13+8.
- Upgrade: The AMI is now based on the 12/01/2021 version of Linux 2 base AMI.

## 2022/01/14 - 1.0.119 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.119` |

- Upgraded AWS Java SDK to 1.12.100.
- Upgraded Jetty to 9.4.44.v20210927.
- Upgraded core.async to 1.5.648.
- Upgraded core.cache to 1.0.225.
- Upgraded http-client to 1.0.110.
- Upgraded transit-clj to 1.0.324.

## 2022/01/14 - 1.0.298 - Ion-dev

| Ion dev |
|---|
| `1.0.298` |

- Upgraded AWS Java SDK to 1.12.100

## 2022/01/14 - 1.0.58 - Ion

| Ion |
|---|
| `1.0.58` |

- Upgraded AWS Java SDK to 1.12.100.

## 2021/09/29 - 936-9118

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `936-9118` | `936-9118` | `936-9118` |

- Fix: ensure that code bucket IAM policy points to the correct bucket if the bucket was deleted and recreated.
- Fix: prevent a problem where in rare situations indexing jobs can fail repeatedly with an `ArityException`.
- Enhancement: improve handling of throttling errors during CloudFormation stack creation.
- Enhancement: the datalog engine will now do self-unification within a single clause.
- Upgraded AWS Java SDK to 1.12.1.
- Upgraded Clojure to 1.10.3.
- Upgraded jackson-core to 2.12.3.
- Upgraded Jetty to 9.4.41.v20210516.
- Upgraded core.async to 1.3.618.
- Upgraded http-client to 1.0.108.
- Upgraded transit-clj to 1.0.324.
- Upgraded data.json to 2.4.0.
- Upgraded tools.namespace to 1.1.0.
- Upgrade: The AMI is now based on the 07/01/2021 version of Linux 2 base AMI.

## 2021/09/29 - 1.0.117 - Client-Cloud Update

| client-cloud |
|---|
| `1.0.117` |

- Upgraded AWS Java SDK to 1.12.1.
- Upgraded Jetty to 9.4.41.v20210516.
- Upgraded core.async to 1.3.618.
- Upgraded core.cache to 1.0.207.
- Upgraded http-client to 1.0.108.
- Upgraded transit-clj to 1.0.324.

## 2021/09/29 - 1.0.294 - Ion-dev

| Ion dev |
|---|
| `1.0.294` |

- Upgraded Lambda runtime to Corretto, per [AWS policy](https://aws.amazon.com/blogs/compute/announcing-migration-of-the-java-8-runtime-in-aws-lambda-to-amazon-corretto/).
- Upgraded AWS Java SDK to 1.12.1.
- Upgraded data.json to 2.4.0.

## 2021/09/29 - 1.0.57 - Ion

| Ion |
|---|
| `1.0.57` |

- Fix: `Nil` error thrown when calling cast before calling `initialize-redirect`.
- Upgraded AWS Java SDK to 1.12.1.
- Upgraded transit-clj to 1.0.324.
- Upgraded data.json to 2.4.0.

## 2021/07/13 - 884-9095

| Storage | Primary Compute | Query Groups |
|---|---|---|
| `884-9095` | `884-9095` | `884-9095` |

- Enhancement: many new [EC2 instance sizes](../../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md#instance-sizes).
- Enhancement: [lower pricing](../../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md#hourly-price) for all instance sizes.
- Enhancement: [API Gateways](../../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#api-gateway) for client and application internet access.
- Enhancement: [run analytics anywhere](../../../08-analytics/03-cloud-configuration/cloud-configuration.md).
- Upgrade: compute nodes have been upgraded from Java 8 to Java 11.
- Replaced: the client access gateway is no longer available; you can connect directly to the new client API gateway.
- Replaced: the analytics gateway is no longer available; you can run your own analytics node or cluster.
- Replaced: the solo tier is no longer available; the production compute stack now scales all the way down to t3.small.
- Replaced: the socks proxy is no longer available; clients can connect directly to the client API Gateway.
- Replaced: each compute group now uses an ALB instead of an NLB.

This upgrade will cause up to one minute of downtime for each compute group. Make sure to perform this upgrade at a time that minimizes the impact of these disruptions.

In addition, make sure to read the upgrade instructions below *for each of the following scenarios that apply to your system today*: solo systems, production systems, developer connections, application connections, analytics connections, and web application (ion) connections.

### 884-9095 for Solo Users

Datomic no longer has a Solo compute stack. If you were using Solo you can upgrade to Production at no additional cost by performing the following steps:

- Your web applications no longer need to go through a [web lambda proxy](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#web-lambda-proxies). They can (and must) instead use [HTTP direct](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#web-ion). HTTP direct is higher performance and simplifies your code, which no longer needs to call `ionize`.
- Perform a [storage upgrade](../../../05-operation/02-cloud/14-upgrading/upgrading.md#storage-only-upgrade).
- Perform a [compute upgrade](../../../05-operation/02-cloud/14-upgrading/upgrading.md#compute-only-upgrade), selecting the t3.small instance size (the default).

### 884-9095 for Production Users

If you are running Production, you now have a set of t3 instance sizes to choose from. Many users will be able to provision smaller, less expensive instances:

- Unless your compute group [needs valcache](../../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md#valcache), you should switch from your current i3 instance to the matching-sized t3 instance.
- If you believe you were already overprovisioned with i3.large, you should consider a [smaller t3 instance size](../../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md#instance-sizes).

### 884-9095 for Developer Connections

884-9095 significantly simplifies developer connections. If you connect to Datomic with a socks proxy to the access gateway, remove all of those steps from your workflow. Instead:

- When you upgrade a compute group, enable the client API gateway.
- Configure your clients to connect directly to the client API gateway endpoint.

### 884-9095 for Application Connections

884-9095 significantly simplifies application connections. If your application connections require managing security groups, VPC endpoints, or VPC peering, remove all of these steps from your workflow. Instead:

- When upgrading a compute group, enable the client API gateway.
- Configure your application clients to connect directly to the client API gateway endpoint.

### 884-9095 for Ion Applications with API Gateway

If you manually created an API Gateway for your ion application on a previous release of Datomic, that gateway will no longer work. You should note any custom configuration for reference and then delete the gateway. Then, let Datomic create the API Gateway for you (preferred), or follow the updated instructions for creating your own API Gateway.

### 884-9095 for Ion Applications using Web Lambda Proxies

If you were using [Web Lambda Proxies](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#web-lambda-proxies), then you should change to HTTP Direct or lambdas. Perform the following:

- Move the function to the `:http-direct` or `:lambdas` key in your ion-config.edn.
- The [ionize](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#ionize) function should not be used.
- Expect the appropriate input data for the type of entry point ([HTTP Direct](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#web-ion) or [lambda](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#lambda-ion)).

### 884-9095 for Analytics Users

If you were using Datomic analytics via the access gateway, you will now need to start your own analytics cluster. [Configure analytics](../../../08-analytics/03-cloud-configuration/cloud-configuration.md) on any machine or cluster of machines (locally or in EC2), and connect to the client API gateway endpoint.

## 2021/07/13 - 884-9095 - Storage Update

Enhancement: The storage template now sets DDB provisioning to fit within the AWS free tier if your usage is low enough.

## 2021/07/13 - 0.10.89 - CLI Tools

| CLI Tools |
|---|
| [0.10.98](https://datomic-releases-1fc2183a.s3.amazonaws.com/tools/datomic-cli/datomic-cli-0.10.98.zip) |

- Add describe-groups system command

## 2021/07/13 - 0.8.113 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.113` |

- Add support for hmac through API Gateway

## 2021/07/13 - 0.9.290 - Ion-dev

| Ion dev |
|---|
| `1.0.290` |

- Update dep list for Datomic Cloud 884-9095
- Enhancement: improved integration between ions and AWS Application Load Balancers for the cloud version release.

## 2021/03/02 - 781-9041 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `781-9041` | `781-9041` | `781-9041` | `781-9041` |

- Upgrade: AWS Lambda Runtimes moved to NodeJs 12.x.
- Enhancement: new AWS Region - `ca-central-1`.
- Upgrade Jetty server to version 9.4.36.v20210114.

## 2021/02/23 - 0.9.282 - Ion-dev & 0.9.50 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.50` | `0.9.282` |

- Ion-dev
  - Improvement: limit how long to wait for a cluster node to gracefully shut down.
  - Improvement: increase performance of Ion request handler.
  - Fix: suppress noisy AsyncContext Alert caused by Ion timeout.
- Ion
  - Improvement: `get-params` now allows arbitrary number of parameters.

## 2021/02/23 - 772-9034 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `772-9034` | `772-9034` | `772-9034` | `772-9034` |

- Fix: [attribute predicates](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#attribute-predicates) are now applied to assertions only.
- Upgrade to presto 348 for [Datomic analytics](../../../08-analytics/04-sql-cli/sql-cli.md).

## 2021/01/20 - 0.8.105 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.105` |

- Improvement: make client dynaload thread-safe.

## 2020/11/30 - 732-8992 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `732-8992` | `732-8992` | `732-8992` | `732-8992` |

- New: change the scale of a [BigDecimal attribute](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#value-types) in a transaction.
- Upgrade to presto 346, fixing a [bug](https://github.com/prestosql/presto/commit/e7eeeedcc9751c022ffb9df648ee5442cd421c32) that prevents analytics gateway from launching.
- Fix: reenable query counts metric in [dashboard](../../../05-operation/01-pro/05-monitoring-and-performance/monitoring-and-performance.md#dashboards).
- Fix: query correctly treats range functions as functions (not as predicates).
- New region: `eu-north-1`.

## 2020/09/23 - 715-8973 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `715-8973` | `715-8973` | `715-8973` | `715-8973` |

- New region: `ap-south-1`.
- Fix: improve storage garbage collection.
- Fix: prevent situation where `tx-range` sometimes returned one extra transaction prior to a specified instant.

## 2020/08/07 - 704-8957 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `704-8957` | `704-8957` | `704-8957` | `704-8957` |

- New feature: [cancel](../../../06-reference/02-transactions/03-processing-transactions/processing-transactions.md#cancel).
- New region: `eu-west-2`.
- Fix: prevent race conditions in Valcache that could lead to a failed read.
- Improvement: better entity predicate error messages.
- Improvement: better indexing performance.
- Upgrade: the version of the Presto server running on the access gateway is now 338. This includes an upgrade to Java 11 on the access gateway.
- Upgrade: the AMI is now based on the 6/17/2020 version of Linux 2 base AMI.
- Upgrade: AWS SDK for Java to version 1.11.826.
- Upgrade: core.async to version 1.3.610.

## 2020/08/07 - 0.9.276 - Ion-Dev & 0.9.48 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.48` | `0.9.276` |

- New feature: [cancel](../../../06-reference/02-transactions/03-processing-transactions/processing-transactions.md#cancel).
- Cloud-deps update.

## 2020/07/21 - 0.8.102 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.102` |

- Fix: `datomic.api.client.async/client` now accepts `:dev-local` as a `:server-type`.

## 2020/07/17 - 0.8.101 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.101` |

- New feature: [dev-local](../../../02-accessing/02-client-library/client-library.md#deps).

## 2020/05/14 - 668-8927 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `668-8927` | `668-8927` | `668-8927` | `668-8927` |

- New feature: [qseq](../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#qseq)
- New feature: [index-pull](../../../04-apis/06-index-pull/index-pull.md)
- New feature: [xform](../../../06-reference/03-query-and-pull/03-pull/pull.md#xform-option)
- Enhancement: improve index efficiency.
- Enhancement: improve performance of pull in queries.
- Enhancement: improve internal record keeping of active databases that could lead to spurious error messages.
- Upgrade: base AMI is now sourced from Amazon Linux 2, and includes recent security updates.

> **Upgrade:** As recommended by AWS, we have upgraded our base AMI to [Amazon Linux 2](https://aws.amazon.com/blogs/aws/update-on-amazon-linux-ami-end-of-life/). This new AMI includes a breaking change to the Linux Netcat utility that Ions use to deploy new code to a cluster. We encourage all users of Ions to upgrade to [version 0.9.265](#0.9.265) or later of ion-dev prior to upgrading to this release.

## 2020/05/14 - 0.9.265 - Ion-Dev & 0.9.43 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.43` | `0.9.265` |

- Enhancement: calculate transitive list of dependencies in [`:dependency-conflict`](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#dependency-conflicts) map.
- Update dep list for [Cloud 668-8927](#668-8927) release.

## 2020/05/14 - 0.8.96 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.98` |

- New feature: [qseq](../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#qseq).
- New feature: [index-pull](../../../04-apis/06-index-pull/index-pull.md).
- Updated jetty dependencies from 9.4.24.v20191120 to 9.4.27.v20200227.

## 2020/04/28 - 0.10.82 - CLI Tools

| CLI Tools |
|---|
| [0.10.82](https://datomic-releases-1fc2183a.s3.amazonaws.com/tools/datomic-cli/datomic-cli-0.10.82.zip) |

- Enhancement: add `--ssho` option to the [gateway restart command](../../../05-operation/02-cloud/07-cli-tools/cli-tools.md#restart) to pass ssh configuration options as if using `ssh -o <option>`.

## 2020/02/21 - 616-8879 - Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `616-8879` | `616-8879` | `616-8879` | `616-8879` |

- New feature: `[:db/retract eid aid]` will retract all values for an eid/aid combination.
- Critical fix: regression introduced in 569-8835 where `datoms`, `seek-datoms`, and `index-range` return incorrect results if `Iterable.iterator` is called more than once on the returned value.
- Fix: respect `nil` as a limit in query `pull` expression.
- Fix: respect `false` as a default in query `pull` expressions for boolean-valued attributes.
- Fix: allow arbitrary `java.util.List` (not just Clojure vectors) for lookup refs.

## 2020/02/19 - 0.10.81 - CLI Tools

| CLI Tools |
|---|
| [0.10.81](https://datomic-releases-1fc2183a.s3.amazonaws.com/tools/datomic-cli/datomic-cli-0.10.81.zip) |

- Many new features - see docs.

## 2020/02/03 - 0.9.251 - Ion-dev

| Ion dev |
|---|
| `0.9.251` |

- Increase timeout for how long a deployment will wait for dependencies to sync on an instance.

## 2020/01/20 - 589-8846 - Storage and Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `589-8846` | `589-8846` | `589-8846` | `589-8846` |

- New region: `sa-east-1`.
- Better tagging of resources created by Datomic.
- Fixed problem using with-db from ions.

## 2019/11/26 - 0.9.33 - CLI Tools

| CLI Tools |
|---|
| [0.9.33](https://datomic-releases-1fc2183a.s3.amazonaws.com/tools/datomic-cli/datomic-cli-0.9.33.zip) |

> This release requires a version of Datomic Cloud 569-8835 or newer.

- Enhancement: `datomic-gateway restart` now just restarts the process running on the access gateway, instead of restarting the whole instance. This results in faster restart times when making configuration changes. Restarting a bastion-only access gateway has no effect.
- Enhancement: automatically retrieve the host key from the access gateway. There will no longer be a warning about the authenticity of the host.

## 2019/11/26 - 569-8835 - Storage and Compute Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `569-8835` | `569-8835` | `569-8835` | `569-8835` |

- Fix: resolve tempids for reference attributes inside tuples.
- Enhancement: performance improvement for range predicates in analytics support.
- Enhancement: SSH public keys automatically handled by access gateway and proxy script.
- Enhancement: AutoScalingGroup details exposed as CloudFormation outputs.
- Enhancement: [JDBC metadata](../../../08-analytics/03-cloud-configuration/cloud-configuration.md#jdbc-metadata) support in analytics.
- Upgrade: AWS Lambda Runtimes moved to NodeJs 10.x.

## 2019/11/19 - 0.9.247 - Ion-dev

| Ion dev |
|---|
| `0.9.247` |

- Better error message for invalid ion-config.edn.
- Update commands to match the preferred approach of [installing ion-dev in your user deps.edn](../../../05-operation/02-cloud/11-how-to/how-to.md#ion-dev).
- Use slf4j-simple to prevent console warnings when invoking tools.

## 2019/11/14 - 0.8.81 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.81` |

- Documentation string updates

## 2019/11/12 - 0.9.240 - Ion-dev

| Ion dev |
|---|
| `0.9.240` |

- Upgrade Clojure tools.deps.alpha to 0.8.584.

## 2019/10/01 - 535-8812 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `535-8812` | `535-8812` | `535-8812` | `535-8812` |

- Fix: prevent exception thrown when handling COUNT(*) clauses in analytics support.
- Fix: do not create redundant `:db.install/attribute` datoms for idempotent schema operations.
- Update transactor Clojure dependency to 1.10.1.
- Enhancement: new AWS Region - ap-northeast-1.

## 2019/10/01 - 0.9.11 - CLI Tools

| CLI Tools |
|---|
| [0.9.11](https://datomic-releases-1fc2183a.s3.amazonaws.com/tools/datomic-cli/datomic-cli-0.9.11.zip) |

- Initial release of [CLI tools](../../../05-operation/02-cloud/11-how-to/how-to.md#cli-tools)

## 2019/10/01 - 512-8806 - Storage and Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `512-8806` | `512-8806` | `512-8806` | `512-8806` |

- New feature preview: analytics. Check [Analytics Concepts](../../../08-analytics/01-analytics-concepts/analytics-concepts.md).
- Fix: allow Datomic Cloud to launch correctly in accounts that have a large number of existing ASGs.

## 2019/08/15 - 0.9.234 - Ion-dev & 0.9.35 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.35` | `0.9.234` |

- Enhancement: allow for the inclusion of the target dir in the `:paths` list.

## 2019/08/06 - 482-8794 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `482-8794` | `482-8794` | `482-8794` | `482-8794` |

- New: upgrade from t2 to t3 instances for bastion and query groups.
- Fix: `tx-range` now returns a map with a `:data` key, consistent with the docs.
- Fix: correctly handle boolean attributes in composite tuples.
- Fix: prevent erroneous attempts to use Datomic On-Prem's excision feature.

## 2019/07/09 - 480-8772 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `480-8772` | `480-8772` | `480-8772` | `480-8772` |

- Fixed problem where databases that change an attribute from `:db.cardinality/one` to `:db.cardinality/many` may become unavailable after a process restart.

## 2019/06/27 - 480-8770 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `480-8770` | `480-8770` | `480-8770` | `480-8770` |

- New feature: [tuples](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#tuples).
- New feature: [attribute predicates](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#attribute-predicates).
- New feature: [entity specs](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#entity-specs).
- New feature: [return maps](../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#return-maps).
- Fix: `sample` aggregate could hang the query thread.

## 2019/06/18 - 0.8.78 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.78` |

- New feature: [return maps](../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#return-maps).
- New feature: reverse references in `nav` (try it in [REBL](http://rebl.cognitect.com/download.html)).

## 2019/06/04 - 0.9.231 - Ion-dev

| Ion dev |
|---|
| `0.9.231` |

- Improvement: fixed issue where some older Java libraries could not be loaded in an ion application.

## 2019/05/16 - 477-8741 - Compute Template Update - HTTP Direct

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `477-8741` | `477-8741` | `477-8741` | `477-8741` |

The 477-8741 release contains fixes, enhancements, and dependency updates:

- Enhancement: [HTTP Direct](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#http-direct-config) integration for ions.
- Enhancement: improved integration between ions and AWS Network Load Balancers. This eliminates transient errors during rolling deployments.
- Enhancement: improved integration between ions and AWS Lambda. This also eliminates transient errors.
- Fix: issue where cluster nodes could become unresponsive when serving multiple databases or a burst of `tx-range` queries.
- Fix: nested queries could deadlock the query pool on Solo nodes.
- Fix: increased direct memory on Production nodes to prevent out-of-direct-memory errors.
- Update: version 1.7.26 of org.slf4j libraries.

## 2019/05/15 - 0.9.229 - Ion-Dev & 0.9.34 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.34` | `0.9.229` |

- New feature: HTTP Direct.
- Enhancement: improved integration between ions and AWS NLBs.

## 2019/04/25 - 470-8654.1 - Compute, Storage, and Query Group Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `470-8654.1` | `470-8654.1` | `470-8654.1` | `470-8654.1` |

> Our Cloud Formation templates use node.js lambdas to create support functions for Cloud Formation. April 30th 2019 is the end-of-life date for AWS Lambda node.js runtime that we currently use.
>
> Effective May 1, 2019, Datomic Cloud CloudFormation templates older than 4/25/2019 will no longer be executed. Datomic systems launched with those templates will continue to run, but any template operations (launching or upgrading CloudFormation stacks) will fail.
>
> All Datomic Cloud users should [upgrade](../../../05-operation/02-cloud/14-upgrading/upgrading.md) to [470-8654.1](#470-8654.1) at their earliest convenience.
>
> For more information see the AWS [runtime support policy](https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html).

Critical: Updated to Node.js 8.10 runtime. All Datomic Cloud users should [upgrade](../../../05-operation/02-cloud/14-upgrading/upgrading.md) at their earliest convenience.

## 2019/02/22 - 470-8654 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `470-8654` | `470-8654` | `470-8654` | `470-8654` |

> If you have created a VPC Endpoint using the provided CloudFormation Template, you will need to delete that CloudFormation Stack before upgrading to this version of Datomic Cloud.
>
> Attempting to upgrade to or past this release without deleting the Stack will result in a failed update with the message: `Export <SystemName>-VpcEndpointServiceName cannot be deleted as it is in use by <SystemName>-vpc-endpoint`
>
> See [the troubleshooting documentation](../../../05-operation/02-cloud/13-cloud-troubleshooting/cloud-troubleshooting.md#vpc-endpoint-stack) for more information.

The 470-8654 release contains fixes, enhancements, and dependency updates:

- Fix: production nodes now use the [documented -Xss values](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#jvm-settings) for starting the JVM, preventing stack overflow when compiling complex ion applications.
- Fix: fixed bug where ion connections could fail to become aware of recent transactions in a timely manner.
- Fix: allow bigint values in transaction data.
- Fix: corrected allowlist handling of function calls from datalog rules.
- Enhancement: new AWS Region - ap-southeast-1.
- Enhancement: improved Valcache cleanup algorithm.
- Enhancement: reduce storage overhead of transactions.
- Enhancement: defer the creation of VPC Endpoint Service until specifically needed for [cross-VPC client access](../../../05-operation/02-cloud/09-vpc-access/vpc-access.md#separate-vpc).
- Update to version 2.9.8 of [Jackson](https://github.com/FasterXML/jackson).
- Update to version 1.11.479 of the [AWS SDK for Java](https://aws.amazon.com/sdk-for-java/).

## 2018/12/10 - 454-8573 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `454-8573` | `454-8573` | `454-8573` | `454-8573` |

The 454-8573 release contains performance and availability enhancements:

- Enhancement: you can configure systems to preload a database before serving requests, eliminating a source of unavailable anomalies.
- Enhancement: ion deployments load active databases before serving requests, eliminating a source of unavailable anomalies.
- Enhancement: improved throughput for transactions initiated by ion applications running the Production Topology.
- Enhancement: improved performance for systems that create and delete many databases (e.g. test systems).
- Enhancement: improvements to logging.

## 2018/12/10 - 0.9.186 - Ion-Dev & 0.9.28 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.28` | `0.9.186` |

- Enhancement: increase CodeDeploy timeout to 5 minutes.
- Enhancement: include doc strings for user-facing functions in the build artifact.

## 2018/11/28 - 0.8.71 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.71` |

- Add compatibility with com.cognitect/aws-api.
- Add Datafy.
- Upgraded http-client to 0.1.87.
- Improved error reporting.

## 2018/10/10 - 441-8505 - Critical Update - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `441-8505` | `441-8505` | `441-8505` | `441-8505` |

Release 441-8505 includes critical updates. All users of the Production Topology and Query Groups should [upgrade](../../../05-operation/02-cloud/14-upgrading/upgrading.md) to 441-8505 immediately. The Solo Topology is not affected.

Critical: fixes a problem that can cause portions of the log to become inaccessible. Please update as soon as possible.

## 2018/09/07 - 441-8477 - Compute Template Update

| Storage | Solo Compute | Production Compute | Query Groups |
|---|---|---|---|
| `441-8477` | `441-8477` | `441-8477` | `441-8477` |

- New feature: [query groups](../../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#query-groups).
- New instance type option: i3.xlarge for [Production](../../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#production-topology).
- Improved memory settings: more stack space for [Solo](../../../05-operation/02-cloud/01-cloud-architecture/cloud-architecture.md#solo-topology), more heap space for Production.
- Bugfix: fixed bug that could prevent a Production node from beginning indexing jobs.

## 2018/08/21 - 0.8.63 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.63` |

- Bugfix: fixed bug that could cause a `with` database query to go to the wrong node in a production cluster.
- Bugfix: fixed Jetty configuration that could cause a client to prevent JVM from shutting down.
- Upgraded transit-clj to 0.8.313.
- `:query-group` parameter is no longer required in the client arg-map.

## 2018/08/15 - 0.9.176 - Ion-Dev & 0.9.26 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.26` | `0.9.176` |

- Enhancement: [parameters](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#parameters) library for working with the AWS systems manager parameter store.

## 2018/08/15 - 409-8407 - Compute Template Update

| Storage | Solo Compute | Production Compute |
|---|---|---|
| `409-8407` | `409-8407` | `409-8407` |

- Bugfix: allow retraction of `:db/unique` attributes.
- Bugfix: coerce `Integer` values to long when needed in transactions.
- Enhancement: better memory utilization allows larger query results.
- Enhancement: automatically rollback deployments when an ion application fails to load.
- Added support for `ap-southeast-2` (Sydney) region.

## 2018/07/10 - 0.9.173 - Ion-Dev & 0.9.14 - Ion

| Ion | Ion dev |
|---|---|
| `0.9.14` | `0.9.173` |

- New Feature: `Datomic.ion.cast` library for [monitoring ions](../../../07-datomic-cloud-ions/10-monitoring-ions/monitoring-ions.md).
- Bugfix: fixed race condition in ion code loading that could allow ion invocation before namespace completely loaded.
- Enhancement: [warn on dependency conflicts](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#dependency-conflicts).
- Improvement: prefer shell-friendly symbols instead of strings as arguments to datomic.ion.dev CLI commands.
- Improvement: better error messaging when [deploying](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#deploy) to the wrong region.
- Improvement: list available deploy groups in [push](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#push) output.
- Improvement: enforce the requirement for `:uname` when project has a `:local/root` in deps.edn.

## 2018/07/02 - 0.8.56 - Client Cloud Update

- Enhancement: added sync to client API. Check [Client Synchronization](../../02-transactions/07-transaction-synchronization/transaction-synchronization.md).
- Better error message when unable to connect to cluster or proxy.

## 2018/06/29 - 402-8396 - Compute Template Update

| Storage | Solo Compute | Production Compute |
|---|---|
| `402-8396` | `402-8396` | `402-8396` |

- Upgraded AWS libs to 1.11.349.
- Upgraded Jackson libs to 2.9.5.
- Fixed cache problem where all `d/with` databases deriving from a common initial call to `d/with-db` had the same common value.

## 2018/06/06 - 0.8.54 - Client-Cloud Update

| client-cloud |
|---|
| `0.8.54` |

- Enhancement: added `:server-type :ion`.
- Enhancement: ensure recentness of `d/db conn`.

## 2018/06/06 - 397-8384 - Storage and Compute Template Update

| Storage | Solo Compute | Production Compute |
|---|---|
| `397-8384` | `397-8384` | `397-8384` |

- Enhancement: [Datomic Ions](../../../07-datomic-cloud-ions/01-ions-overview/ions-overview.md).
- Improvement: Replaced Application Load Balancer with Network Load Balancer. If your applications run in a separate VPC you will need to [configure a VPC endpoint](../../../05-operation/02-cloud/09-vpc-access/vpc-access.md#inter-vpc).

## 2018/02/21 - 303-8300 - Storage and Compute Template Update

| Storage | Solo Compute | Production Compute |
|---|---|
| `303-8300` | `303-8300` | `303-8300` |

- Bugfix: doubles and floats allowed in transactions.
- Bugfix: avoid unnecessary ":AdopterSkippedOlder" alert when creating a new database.
- Update: latest Amazon Linux patches.
- Improvement: better error handling in the storage template.
- Improvement: reduce memcached timeout.

## 2018/01/15 - 297-8291

- Initial release.

| Storage | Solo Compute | Production Compute |
|---|---|---|
| `297-8291` | `297-8291` | `297-8291` |

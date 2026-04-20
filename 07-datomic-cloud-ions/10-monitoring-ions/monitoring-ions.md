# Monitoring Ions

## Topics

- [Overview](#overview)
- [Events](#events)
- [Alerts](#alerts)
- [Dev](#dev)
- [Metrics](#metrics)
- [Local development workflow](#local-development-workflow)
- [Java logging](#java-logging)

All the code examples shown below are available in the [ion event example](https://github.com/Datomic/ion-event-example) project.

## Overview

AWS CloudWatch provides a powerful set of tools for monitoring a software system running on AWS, and Datomic [fully integrates with these tools](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md).

The `datomic.ion.cast` namespace lets ion application code add your own monitoring data alongside the monitoring data already being produced by Datomic applications. Cast supports four categories of monitoring data:

- [Event](#events): ordinary occurrence that is of interest to an operator, such as start and stop events for a process or activity.
- [Alert](#alerts): extraordinary occurrence that requires operator intervention, such as the failure of some important process.
- [Metric](#metrics): a numeric value in a named time series, such as the latency for an operation.
- [Dev](#dev): information of interest during development, e.g. fine-grained logging to troubleshoot a problem during development. Dev data can be much higher volume than events or alerts.

The cast is part of the [com.datomic/ion](../../11-releases/releases.md) library.

## Events

An event is an ordinary occurrence that is of interest to an operator, such as start and stop events for a process or activity. The `event` function takes a map with a single required key, `:msg`, a string, plus any additional namespace-qualified keys you choose.

For example, the `event` call below logs the raw JSON sent to an AWS lambda ion:

```clojure
(cast/event {:msg "CodeDeployEvent" ::json input})
```

When the ion code is running on Datomic Cloud, events are posted to [Datomic's CloudWatch log](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md#logs-produced-by-datomic-cloud). Datomic transforms data to [searchable JSON](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md#finding-logs-by-message) as follows:

- Datomic adds the key/value pair `{"Type" "Event"}`
- Keywords are converted to CamelCase strings, with the delimiters `.`, `/`, and `_` introducing a new capital letter – for example, `my.app/key` becomes "MyAppKey"
- Exceptions are converted to data via Clojure's `Throwable->map`
- `Double/Nan` and `Double/Infinity` are converted to the strings "NaN" and "Infinity"
- Other datatypes are converted to strings via Clojure's `str` function

## Alerts

An alert is an extraordinary occurrence that requires operator intervention, such as the failure of some important process. The `alert` function takes a map with:

- A required `:msg` string
- An optional `:ex` Java Throwable
- Any additional namespace-qualified keys you choose

For example, the `alert` call below is used in the catch block of a web service ion to report an unexpected server failure:

```clojure
(cast/alert {:msg "SlackHandlerFailed" :ex t})
```

When the ion code is running on Datomic Cloud, alerts are posted to [Datomic's CloudWatch log](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md). The data transformation is the same as for [events](#events), except that the value for `Type` is "Alert".

In addition to the log entry, alerts create a [Datomic CloudWatch metric](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud) value named "Alerts." You can use the occurrence of this metric to e.g. send an SNS message to an operator.

## Metrics

To create your own metrics, enable detailed metrics for your compute group. This is the default for EC2 instances "large" and larger. For smaller instance sizes, choose the "detailed" metrics level in your [CloudFormation template parameters](../../05-operation/02-cloud/05-compute-templates/compute-templates.md#parameters).

A metric is a numeric value in a named time series, such as the latency for an operation. The `metric` function takes a map with the following required keys:

- `name`, a keyword
- `value`, a number that can be cast via Clojure `double`
- `units`, one of `:msec`, `:bytes`, `:kb`, `:mb`, `:gb`, `:sec`, `:count`

For example, the `metric` call below is used to record the occurrence of a CodeDeployEvent:

```clojure
(cast/metric {:name :CodeDeployEvent :value 1 :units :count})
```

> AWS will display all metrics in lowercase with the first character capitalized. As an example, the aforementioned `:CodeDeployEvent` will display as `Codedeployevent` in both the metrics and the logs. Additionally, CloudWatch Metric names do not have namespaces, and any namespace provided in the metric name will be ignored.

Choose metric names that do not collide with Datomic's [built-in metric names](../../05-operation/02-cloud/12-monitoring-cloud/monitoring-cloud.md#metrics-produced-by-datomic-cloud).

## Dev

`dev` is information of interest during development, e.g. fine-grained logging to troubleshoot a problem during development. Dev data can be much higher volume than events or alerts.

The `cast/dev` function takes an arbitrary map. In the example below, `cast/dev` shows the channel and text of a Slack post (presumably so that a developer can verify that a function is being called correctly):

```clojure
(cast/dev {:msg "PostingToSlack" ::channel channel ::text text})
```

You can redirect `cast/dev` as part of your [local dev workflow](#local-development-workflow).

> Configuring a destination for `cast/dev` when running in Datomic Cloud is currently not supported, and `dev` calls do not post to CloudWatch.

## Local development workflow

You may find it useful to see monitoring data when you are developing on a machine outside Datomic. You can call `initialize-redirect` once per process to direct all `alert`, `event`, and `dev` output to one of the following targets:

- `:stdout`     Standard output
- `:stderr`     Standard error
- (String)      A filename

Alternatively, if you are running Clojure 1.10 or greater, you can send all `alert`, `event`, and `dev` output to registered taps by calling `initialize-redirect` with a target of `:tap`.

There is no redirection when running in Datomic Cloud, so it is ok to leave calls to `initialize-redirect` in your production code.

While running in Cloud, calls to `dev` are not posted to CloudWatch.

## Java logging

Datomic Cloud uses [SLF4J](https://www.slf4j.org/index.html) to redirect output from all Java logging frameworks to `cast/alert`:

- `ERROR` and `WARN` level logs produce an alert whose message includes the logger name, level, and message
- Lower log levels are ignored

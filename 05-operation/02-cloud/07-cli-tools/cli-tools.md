# CLI Tools

- [General Usage](#general-usage)
- [Log](#log)
- [System](#system)
- [Cloud](#cloud)
- [Legacy](#legacy)

## General Usage

The Datomic CLI tool is distributed as a zip file containing a single program. Download the latest version of the tool from the [releases page](../../../11-releases/releases.md) and check the documentation on this page.

`Datomic` utilizes the sourced or specified [AWS Credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) to operate or retrieve information about your Datomic system.

The instructions here assume that you have [AWS administrator](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html#jf_administrator) permissions.

Information about the command can be seen using the help command after the specific command or sub-command:

- `datomic cloud help`
- `datomic cloud list-systems help`

Supplied options are passed *after* the required information for the command.

### General Options

Most commands support the following options:

- `-h` **or** `--help` - Specify `<command> help` for help about a command
- `-p` **or** `--profile` - named AWS credentials to use for accessing the AWS account
- `-r` **or** `--region` - AWS region of the Datomic system

## Log

### List

Queries the supplied Datomic Compute Group for logs.

`Log` requires one argument, a [`compute-group-name`](../11-how-to/how-to.md#compute-group-name):

```
datomic log list <compute-group>
```

- `-f` **or** `--filter` - `alerts` by default or `all` if specified.
- `-t` **or** `--tod` - logs from the specified time provided as a [Clojure #inst literal](https://clojure.org/reference/reader#_built_in_tagged_literals) to the value of `--minutes-back`. Defaults to the current time.
- `-m` **or** `--minutes-back` - logs from `--tod` to an integer number of minutes in the past. Defaults to 20 minutes.
- `-i` **or** `--instance-id` - Limit log results to EC2 instances with this prefix.
- [General options](#general-options).

### Detail

Returns the details of a specific log. Returns a JSON array.

`Detail` requires two arguments, a [`compute-group-name`](../11-how-to/how-to.md#compute-group-name) and a [message-id](#list) supplied to `-m`:

```
datomic log detail <compute-group> -m <message-id>
```

- `-m` **or** `--msg` - message id (provided by [log list](#list) command)
- `-i` **or** `--instance-id` - only show logs from compute groups with a prefix matching the supplied string
- [General options](#general-options)

## System

### List-instances

`List-instances` displays the [EC2 instances](https://aws.amazon.com/ec2/instance-types/), with their instance-id and current status, that are utilized by the supplied `system-name`:

```
datomic system list-instances <system>
```

[General options](#general-options).

### Describe-groups

`Describe-groups` displays information about the compute groups of the supplied Datomic Cloud system:

```
datomic system describe-groups <system>
```

A JSON array of compute group information is returned:

```js
[{"name": "<group-name>",
  "type": "<group-type>",
  "endpoints":
  [{"type": "<endpoint-type>",
    "api-gateway-endpoint": "<endpoint-address>",
    "api-gateway-id":" <gateway-id>",
    "api-gateway-name": "<gateway-name>"},
   ...],
  "cft-version": "<cloudformation-version>",
  "cloud-version": "<cloud-version>"},
 ...]
```

## Cloud

### List-systems

Lists all currently active and inactive Datomic systems on the sourced or specified account.

```
datomic cloud list-systems
```

[General options](#general-options).

## Legacy

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#781-9041) and lower.

### Client

The `client` commands use ssh to allow connections from outside the [Datomic VPC](../01-cloud-architecture/cloud-architecture.md#security) through the [access gateway](../01-cloud-architecture/cloud-architecture.md#api-gateways) to a [Datomic system](../01-cloud-architecture/cloud-architecture.md#system).

`client` commands requires a single argument: [`system-name`](../11-how-to/how-to.md#system-name).

#### Access

The `access` uses ssh to allow connections from outside the [Datomic VPC](../01-cloud-architecture/cloud-architecture.md#security) to the [access gateway](../01-cloud-architecture/cloud-architecture.md#api-gateways) to a [Datomic system](../01-cloud-architecture/cloud-architecture.md#system).

The `access` script continues to run once launched. If the script terminates (as it might e.g. connection loss after laptop sleep), you will need to restart it.

```
datomic client access <system>
```

The command above opens a SOCKS proxy to the access gateway in the system for Datomic client access. Note that:

- `--port` - local port to use for forwarding. Default 8192.
- `--ssho` - pass ssh configuration options as if using `ssh -o <option>`.
- [General options](#general-options).

### Gateway

The access gateway runs inside a Datomic system's private VPC. It acts as an entry point for Client API and analytics connections from outside the VPC.

While the gateway runs automatically based on your CloudFormation template, there are some situations where you may want to explicitly control it:

- You can start or stop the gateway to control access or EC2 instance costs
- You can restart the gateway to recover from a process failure
- You can restart the gateway to pick up new analytics catalog settings

You can also monitor the status of the access gateway in the [AWS EC2 Management Console.](https://console.aws.amazon.com/ec2/#Instances:search=-bastion;sort=desc:launchTime) The access gateway is named *SystemName*-bastion. Note that:

- When the `Instance State` is `running`, the access gateway is available for use.
- An `Instance State` of `terminated` indicates the access gateway has terminated. You will incur no future charges for that instance.

#### Enable

Enable the access gateway, setting the ASG to 1.

`Enable` requires a single argument, [`system-name`](../11-how-to/how-to.md#system-name):

```
datomic gateway enable <system>
```

[General options](#general-options).

#### Disable

Disable the access gateway, setting the ASG to 0.

`Disable` requires a single argument, [`system-name`](../11-how-to/how-to.md#system-name):

```
datomic gateway disable <system>
```

[General options](#general-options).

#### Restart

Restarts the analytics support on the access gateway.

`Restart` requires a single argument, [`system-name`](../11-how-to/how-to.md#system-name):

```
datomic gateway restart <system>
```

[General options](#general-options).

### Solo

`Solo` manages the [ASG](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) for a Datomic solo instance.

`Solo` commands requires a single argument, [`system-name`](../11-how-to/how-to.md#system-name).

#### Up

Sets the [ASG](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) for the specified Datomic solo system to 1:

```
datomic solo up <system>
```

- `--wait` - Wait until the command against the instances has finished
- [General options](#general-options)

#### Down

Sets the [ASG](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) for the specified Datomic solo system to 0:

```
datomic solo down <system>
```

- `--wait` - Wait until the command against the instances has finished
- [General options](#general-options)

#### Reset

Terminates the Datomic solo system node and access gateway, which allows [the ASG](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) to start new instances:

```
datomic solo reset <system>
```

- `--wait` - Wait until the command against the instances has finished.
- [General Options](#general-options)

### Analytics

Tools for utilizing [Datomic analytics](../../../08-analytics/01-analytics-concepts/analytics-concepts.md).

#### Access

`Access` uses SSH to allow connections from outside the [Datomic VPC](../01-cloud-architecture/cloud-architecture.md#security) through the [access gateway](../01-cloud-architecture/cloud-architecture.md#api-gateways) to a [Datomic system](../01-cloud-architecture/cloud-architecture.md#system) for [analytics support](../../../08-analytics/01-analytics-concepts/analytics-concepts.md).

`Access` requires a single argument, [`system-name`](../11-how-to/how-to.md#system-name):

```
datomic analytics access <system>
```

- `--port` - local port to use for forwarding. Default 8989.
- `--ssho` - pass ssh configuration options as if using `ssh -o <option>`.
- [General options](#general-options).

#### Sync

`Sync` syncs your local [configuration directory](../../../08-analytics/03-cloud-configuration/cloud-configuration.md#configuration-directory) to the Datomic system's analytics configuration.

`Sync` requires two arguments, [`system-name`](../11-how-to/how-to.md#system-name) and your local configuration directory:

```
datomic analytics sync <system> <directory>
```

- `-q` **or** `--query-group` - Datomic Query Group to target for sync (defaults to the primary compute group)
- [General options](#general-options)

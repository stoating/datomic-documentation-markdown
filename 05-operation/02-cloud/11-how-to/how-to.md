# How To

- [Install the AWS CLI](#install-the-aws-cli)
- [Manage AWS Access Keys for Datomic](#manage-aws-access-keys-for-datomic)
- [Install Clojure CLI](#install-clojure-cli)
- [Install Ion-Dev Tools](#install-ion-dev-tools)
- [Install Ion Library](#install-ion-library)
- [Delete Ion lambdas](#delete-ion-lambdas)
- [Control Shell Scripts](#control-shell-scripts)
- [Find Datomic System Name](#find-datomic-system-name)
- [Find Datomic Nodes](#find-datomic-nodes)
- [Find Compute Group Name](#find-compute-group-name)
- [Find Datomic Application Name](#find-datomic-application-name)
- [Find System S3 Bucket](#find-system-s3-bucket)
- [Find Ion Code Bucket](#find-ion-code-bucket)
- [Find Template Outputs](#find-template-outputs)
- [Upgrade Base Schema](#upgrade-base-schema)
- [Legacy](#legacy)

## Install the AWS CLI

[Install the AWS command line interface](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html). Make sure you have the version 1.11.170 or later. If you are unfamiliar with managing Python environments, first try using the bundled [AWS CLI installer](https://docs.aws.amazon.com/cli/latest/userguide/awscli-install-bundle.html). You can check your version using:

```
aws --version
```

> Running the `datomic-socks-proxy` script with an earlier version of the AWS CLI will result in a [generic error message](../13-cloud-troubleshooting/cloud-troubleshooting.md#unsupported-aws-cli-version) and failure of the script.

If you require multiple versions of the AWS CLI to be installed, then investigate [using Python's virtual environments](https://packaging.python.org/tutorials/installing-packages/#creating-virtual-environments), or the third-party [virtualenv solution](https://virtualenv.pypa.io/en/latest/).

## Manage AWS Access Keys for Datomic

The Datomic [CLI tools](../07-cli-tools/cli-tools.md) and [ion deployment tools](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#deploy) require that you have a set of AWS access keys with permissions to access Datomic. To create these keys, you must have an [authorized IAM user](../06-access-control/access-control.md). With this user you can then:

- [Create access keys](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)
- [Add access keys to your environment](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-quick-configuration)

Datomic supports the use of [named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html) as a credentials source. Additional information about IAM Users and access keys can be found in the [AWS security credentials documentation](http://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys).

## Install Clojure CLI

Datomic's tools use the [Clojure CLI](https://clojure.org/guides/deps_and_cli), version 1.10.1.727 or later. You can use `clojure -Sdescribe` to check your version of the Clojure CLI.

The Clojure [getting started](https://clojure.org/guides/getting_started#_clojure_installer_and_cli_tools) page has instructions for installing and upgrading Clojure.

## Install Ion-Dev Tools

The Datomic ion-dev tools should be installed via an alias in your [user deps.edn file](https://clojure.org/reference/deps_and_cli#_directories), which is normally located at `$HOME/.clojure/deps.edn`.

- To install the tools, add the `datomic-cloud` maven repo under your `:mvn/repos` key:

```clojure
"datomic-cloud" {:url "s3://datomic-releases-1fc2183a/maven/releases"}
```

- Then add an `:ion-dev` entry under your `:aliases` key with the [latest version](../../../11-releases/releases.md) of ion-dev:

```clojure
:ion-dev
{:deps {com.datomic/ion-dev {:mvn/version "1.0.352"}}
 :main-opts ["-m" "datomic.ion.dev"]}
```

The ions tutorial includes a [complete .clojure/deps.edn example](https://github.com/Datomic/ion-starter/blob/master/examples/.clojure/deps.edn).

> Note that `ion-dev` configures logging to stderr via [slf4j-simple](http://www.slf4j.org/api/org/slf4j/impl/SimpleLogger.html). This is probably adequate for most scenarios, but you, of course, have the [full power of SLF4J](http://www.slf4j.org/manual.html) at your disposal.

## Install Ion Library

The Datomic ion library has to be installed in the deps.edn for each ion project:

- Add the `datomic-cloud` maven repo under your `:mvn/repos` key:

```clojure
"datomic-cloud" {:url "s3://datomic-releases-1fc2183a/maven/releases"}
```

- Then add the [latest version](../../../11-releases/releases.md) of ion-dev to your `:deps`:

```clojure
com.datomic/ion {:mvn/version "1.0.71"}
```

## Delete Ion lambdas

You do **not** need to delete the AWS lambdas managed by Datomic Ions to control cost. AWS lambda pricing is entirely usage-based, so if you do not invoke lambdas, they cost nothing. If you want to delete these Lambdas anyway, you can [push](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#push) and [deploy](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#deploy) an application with an empty `lambdas` map in [ion-config.edn](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#ion-config).

## Control Shell Scripts

Some Datomic CLI tools (e.g. the [client access](../07-cli-tools/cli-tools.md#access) script) continue to run in the foreground once you launch them. For interactive use, the easiest way to manage such tools is simply to kill them with Ctrl-C when they are no longer needed.

If you are building automation around scripts, you can of course use all the ordinary Unix facilities, e.g.

- You can use `pkill [script-name]` to kill a script from another terminal
- You can use e.g. `nohup [script-command] [script-args] &` to launch a script in the background, directing its output to `nohup.out`
  - Currently running processes can be listed with `jobs` and brought to the foreground with `fg $pid` where pid is the process number listed in `jobs`

## Find Datomic System Name

Every Datomic system resides in a single AWS region and has a unique system name within that region. The system name is the Stack Name used to [launch the Datomic storage stack](../04-storage-template/storage-template.md#stack-name).

If you did not [start Datomic](../02-start-a-system/start-a-system.md) yourself, you can find the available systems from the CLI or the AWS Console:

- From the CLI, use the [`datomic cloud list-systems`](../07-cli-tools/cli-tools.md#list-systems) command
- From the console browse to [CloudFormation console](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)

## Find Datomic Nodes

You can list the names of all running Datomic nodes using:

- Utilize the [`datomic system list-instances SYSTEM`](../07-cli-tools/cli-tools.md#list-instances) command, replacing `SYSTEM` with your system name

or

- Utilize the following [AWS CLI](#install-the-aws-cli) command, replacing `[region]` with the region you want to query:

```sh
aws ec2 describe-instances --region [region] --filters "Name=tag-key,Values=datomic:tx-group" "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].[Tags[?Key==`datomic:system`].Value]' --output text
```

## Find Compute Group Name

If you start a Datomic system with [split stacks](../16-splitting-stacks/splitting-stacks.md), then it has one or more compute groups. A compute group's name is the name of the CloudFormation stack you used to [create the group](../05-compute-templates/compute-templates.md#stack-name).

You can query compute groups for a system from the CLI, or browse them in the AWS Console:

- From the CLI, use the [`datomic system list-instances SYSTEM`](../07-cli-tools/cli-tools.md#list-instances) command
- Browse all your CloudFormation stacks in the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)

## Find Datomic Application Name

Every Datomic [compute group](../01-cloud-architecture/cloud-architecture.md#compute-groups) has an associated [application](../01-cloud-architecture/cloud-architecture.md#applications) for ion deployments. You specify the application name when you [create a compute group](../05-compute-templates/compute-templates.md#stack-name).

If you did not start the compute group, you can find the application name by browsing your compute group in the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active) and looking for the `ApplicationName` value in the Parameters tab.

## Find System S3 Bucket

Every Datomic system has its own S3 bucket for configuration and data storage.

You can find this S3 bucket by browsing the Outputs tab of your [storage stack](../04-storage-template/storage-template.md#stack-name) in the [AWS Console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active&tab=outputs) and looking for the `S3DatomicArn` output.

## Find Ion Code Bucket

All Datomic systems in a region share a common S3 bucket for [ion](../../../07-datomic-cloud-ions/01-ions-overview/ions-overview.md) deployment. This bucket has a `datomic:code` AWS tag.

You can find this bucket by creating a [resource group](https://console.aws.amazon.com/resource-groups/groups/new) in the AWS Console:

- Make sure you are working in the region you want to query
- Create a tag-based resource group
- Select a resource type of `AWS::S3::Bucket`
- Search for a tag named `datomic:code`
- Give your group a name, e.g. `DatomicCodeBucket`
- Create your group

## Find Template Outputs

Datomic's CloudFormation templates create [outputs](https://docs.amazonaws.cn/en_us/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) describing the resources that they manage. To view these outputs, go to the [CloudFormation console](http://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active), or query them from the AWS CLI. The following example query lists the outputs for a stack named "inventory-primary":

```sh
aws cloudformation describe-stacks --stack-name inventory-primary
```

Information about the compute groups of a system can be found using `describe-groups`:

```
datomic system describe-groups SYSTEM
```

## Upgrade Base Schema

Every Datomic database starts with a base schema, whose identifiers are prefixed with `:db`. When the Datomic team enhances the base schema, all databases created with new versions of Datomic get the enhancements automatically.

Existing databases do **not** automatically incorporate enhancements to the base schema. To upgrade existing databases to use new base schema, first upgrade **all** compute groups (primary and query) to the minimum version of Datomic required for the base schema features you want:

| Feature | Minimum Datomic version | Released |
| --- | ---: | --- |
| Tuples | 480-8770 | June 27, 2019 |
| Attribute predicates | 480-8770 | June 27, 2019 |
| Entity specs | 480-8770 | June 27, 2019 |
| db.type/symbol | 480-8770 | June 27, 2019 |

Once you have upgraded all compute groups, you can upgrade an existing database to use the latest base schema by passing the `:upgrade-schema` action to `administer-system`. For example:

```clojure
(d/administer-system client {:db-name "movies"
                             :action :upgrade-schema})
```

Upgrading a schema is an idempotent operation, so it will not harm your data to call it more than once. That said, treat `administer-system` as a potentially expensive operation. Call it only when you need it, not before every call to `connect`.

> Once you have used a base schema feature in a particular database, do **not** run any compute group for that system on versions of Datomic older than the features used. See the table above.

## Legacy

### Check System Topology (Legacy)

All Datomic systems include one or more CloudFormation stacks. To check whether a system uses the [solo or production topology](../01-cloud-architecture/cloud-architecture.md#primary-compute-stack) either:

- Utilize the [`datomic cloud list-systems`](../07-cli-tools/cli-tools.md#list-systems) command.

or

- Find your system's [primary compute group](#find-compute-group-name) in the CloudFormation console.
- If the CloudFormation template has a `DatomicProductionCompute` key in its Outputs, the system is running the *production* topology. If the key is absent, the system is running *solo*.

### Convert from Solo to Production (Legacy)

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#20210302-781-9041-compute-update) and lower.

To update your system from solo to production [topology](../01-cloud-architecture/cloud-architecture.md#high-availability):

- You must be running [split stacks](../16-splitting-stacks/splitting-stacks.md)
- Click the name of your solo stack in [CloudFormation console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active)
- Click the "Update" button

On the following page, select "Specify an Amazon S3 template URL:" and enter the CloudFormation template URL for the latest production template you wish to upgrade to (see the [release page](../../../11-releases/releases.md) for the latest production template) and click "Next".

- On the "Specify details" screen, leave all options unchanged.
- On the "Options" screen, leave all options unchanged.
- On the "Review" screen, click the mandatory checkboxes that you agree with. You may need to scroll down.

Wait for the template to report UPDATE_COMPLETE. This can take ten or more minutes. You can refresh the CloudFormation dashboard to see progress. In the [EC2 dashboard](https://console.aws.amazon.com/ec2/v2/) you'll note that two i3.large instances with your stack name are running and a t2.small instance has been terminated. Your production stack is now ready for use.

# Access Gateway (Legacy)

> The access gateway is deprecated and is no longer available in
> Datomic Cloud. This page only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#781-9041) and lower.

The access gateway provides two capabilities: client access from
outside the VPC, and Datomic analytics. If you are planning a new
system, you should **not** use the access gateway. Instead, you should
use an [API Gateway](../01-cloud-architecture/cloud-architecture.md#api-gateways) for client access, and [bring your own analytics
cluster](../../../08-analytics/03-cloud-configuration/cloud-configuration.md).

The access gateway is an EC2 instance that is managed by the primary
compute group, runs in the same VPC as your Datomic compute groups.

## Client Access

### Allow Inbound Traffic to the Access Gateway

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#781-9041) and lower.

Datomic runs in a private VPC. To allow access from outside the Datomic VPC
(e.g. for developers), you must add an inbound rule to the
[access gateway](../01-cloud-architecture/cloud-architecture.md#api-gateways) security group.

> **NOTE:** Access gateway instances are secured by a
> keypair that is accessible to Datomic administrators. If you want to
> further restrict access by IP also, you can enter a specific IP address or range
> of addresses for *Source* in the instructions below.

- Navigate to the Security Groups section of the [AWS EC2 Management Console](https://console.aws.amazon.com/ec2/#SecurityGroups:search=-bastion)
- Click on the access gateway security group for your Datomic Cloud system, named *<system-name>-bastion*
- Click the "Inbound" tab in the Security Group details at the bottom of the console
- Click "Edit Inbound Rules" to display the Edit inbound rules dialog box
- Add an entry with the following parameters:
  - Type: SSH
  - Protocol: TCP
  - Port Range: 22
  - Source: Select "Anywhere" from the source dropdown
- Accept the defaults for the other entries, and click "Save Rules"

### Prerequisites

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#781-9041) and lower.

This page assumes that an administrator has already:

- [Started a Datomic System](../02-start-a-system/start-a-system.md)
- [Configured user access](../06-access-control/access-control.md)
- [Allowed inbound traffic](#allow-inbound-traffic-to-the-access-gateway)

Before you configure Client API connectivity to a Datomic database from outside the VPC, you need to:

- Install [Clojure](https://clojure.org/guides/getting_started) 1.9 or later
- Install the [AWS CLI](../11-how-to/how-to.md#install-the-aws-cli) version 1.11.170 or greater
- Install the [Datomic CLI Tools](../07-cli-tools/cli-tools.md)
- Configure a shell environment with [AWS access keys](../11-how-to/how-to.md#manage-aws-access-keys-for-datomic) for Datomic
- Know the [region and name](../11-how-to/how-to.md#find-datomic-system-name) of the Datomic System you want to connect to

### Connect to the Access Gateway

To connect to the access gateway, run the [datomic access](../07-cli-tools/cli-tools.md#access) command to create an SOCKS proxy connection, passing you [system
name](../11-how-to/how-to.md#find-datomic-system-name):

```sh
datomic client access <system>
```

The script will continue to run once launched.

### Test Access Gateway Connection

Run the following command to test your system's ability to reach
Datomic through the SOCKS proxy, replacing `[system]`, `[region]`. and
`[port]` with your [system-name, region](../11-how-to/how-to.md#find-datomic-system-name) and port.

```sh
curl -x socks5h://localhost:[port] http://entry.[system].[region].datomic.net:8182/
```

On success, this command will return text like:

```clojure
{:s3-auth-path [bucket-name]}
```

Any other response indicates a failure to connect. Carefully review
the prerequisites and steps on this page. If you feel that you have
completed each step correctly, check the
[troubleshooting documentation](../13-cloud-troubleshooting/cloud-troubleshooting.md#troubleshooting-socks-proxy-errors).

If you are trying Datomic for the first time, a good next step is the
[Client API Tutorial](../../../03-tutorials/02-client-tutorial/client-tutorial.md).

## Analytics

> This section only applies to [Datomic 781-9041](../../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#781-9041) and lower.

For the initial setup of analytics support, you need to do three things:

- [Enable](#enable) analytics in CloudFormation
- Create a local [configuration directory](#configuration-directory) and [sync](#syncing-configuration) it to your Datomic
system
- [Restart](#restarting-access-gateway) the [access gateway](../01-cloud-architecture/cloud-architecture.md#api-gateways) to pick up the initial configuration

After you configure analytics support, you can [connect](../../../08-analytics/15-connecting-cloud-legacy/connecting-cloud-legacy.md) analytics tools.

Later, you may revisit the configuration for the following reasons:

- [Update](#updating-instance-type) the access gateway instance type to match your performance
needs
- Update the [catalog](#catalog) to change the databases exposed by analytics
- Update [metaschemas](#metaschema) to change the mapping from Datomic databases to SQL tables

### Enable

Analytics requires an access gateway instance with enough memory to
run the analytics process. To enable analytics support:

- Ensure that your Datomic system has [separate storage and compute stacks](../16-splitting-stacks/splitting-stacks.md#rationale)
- Ensure that your compute group [specifies an access-gateway](../05-compute-templates/compute-templates.md)
instance type that is larger than `nano`

You can choose a different access gateway instance type at any time. Note that
analytics support will be unavailable while the new instance launches
(typically for a few minutes).

#### Updating Instance Type

You can change the access gateway instance type from the
[CloudFormation console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active).

- Select your compute stack via the checkbox or radio button
- Click the "Actions" button and select "Update Stack"
- On the "Select Template" screen, choose "Use current template"
- On the "Specify Details" screen, change your access gateway instance type
- On the "Options" screen, leave all options unchanged
- On the "Review" screen, click the checkbox stating "I acknowledge that
AWS CloudFormation might create IAM resources with custom names" and click "Update"

Wait for the template to report UPDATE_COMPLETE. This can take a few minutes.

### Configuration Directory

Analytics support requires you to configure a [catalog](../../../08-analytics/01-analytics-concepts/analytics-concepts.md#sql-mapping) and zero or more
[metaschema](../../../08-analytics/01-analytics-concepts/analytics-concepts.md#metaschemas) files locally, and then [sync](#syncing-configuration) them to your running Datomic system.

The configuration directory must contain two subdirectories, `datomic` and `catalog`:

```
my-config-dir/
|-- catalog
|   `-- my-system.properties
`-- datomic
    `-- my-metaschema.edn
```

Populate the catalog and metaschema files as described below.

### Catalog

The `catalog` subdirectory of your [local configuration directory](#configuration-directory)
should contain a `[your-catalog-name].properties` file with the contents:

```
connector.name=datomic

#optional - limit exposed dbs by listing them here:
#datomic.databases=[thisdb thatdb]
```

Replace `[your-catalog-name]` with a name of your choice. (Since the
catalog corresponds to a Datomic system, your Datomic [system name](../11-how-to/how-to.md#find-datomic-system-name) is
often a good choice.) The catalog name will be used in any subsequent
configurations or URIs that mention a "catalog" or "Presto catalog"
name.

The optional list of exposed databases (`datomic.databases`) enables
you to select only specific databases from this system as accessible
through the access gateway. If you leave this list commented, **all**
databases in the system will be accessible via analytics. Limiting the
set of exposed databases to those specifically desired for analytics
is highly recommended.

After you change the catalog, you must [sync](#syncing-configuration) the configuration files
and then [restart](#restarting-access-gateway) the access gateway.

### Enabling JDBC Metadata

Many analytics tools provide the ability to automatically explore all
of the tables in your system. Such exploration involves
issuing one (or many!) JDBC metadata queries, forcing the load of every
table in your system.

Because these automatic queries can be so expensive, they are disabled by
default. To enable JDBC metadata queries, you must explicitly enumerate the Datomic
databases you want to query with the `datomic.databases` property
[described above](#catalog).

### Metaschema

Place zero or more [metaschema EDN files](../../../08-analytics/01-analytics-concepts/analytics-concepts.md#metaschemas) in the `datomic` subdirectory
of your [local configuration directory](#configuration-directory).

After you call [sync](#syncing-configuration), your metaschema changes will be available in a
minute or less. This facilitates the interactive development of your
metaschema:

- You do **not** need to restart the access gateway
- You do **not** need to restart any [analytics tools](../../../08-analytics/13-other-tools/other-tools.md)

If your analytics tool scans the database to discover the schema, you
may need to re-run this scan manually to pick up schema changes.

### Syncing Configuration

The `analytics sync` command in the [datomic tools](../07-cli-tools/cli-tools.md) updates analytics
configuration to match your local [configuration directory](#configuration-directory). Call `sync` with
your [system name](../11-how-to/how-to.md#find-datomic-system-name) and configuration dir:

```
datomic analytics sync <system> <path-to-config-dir>
```

The access gateway performs queries against your Datomic system, using
the [primary compute](../01-cloud-architecture/cloud-architecture.md#primary-compute-stack) group by default. If you find that analytics
queries are competing for resources with other uses of your system,
you can create a dedicated [query group](../01-cloud-architecture/cloud-architecture.md#query-groups) for analytics, and
configure analytics to use this dedicated query group with the `-q`
option to `sync`

```
datomic analytics sync <system> <path-to-config-dir> -q <query-group-name>
```

### Restarting Access Gateway

To restart the access gateway, pass `restart` and your system name to
the [datomic gateway](../07-cli-tools/cli-tools.md#restart) [CLI tool](../07-cli-tools/cli-tools.md).

```
datomic gateway restart <system>
```

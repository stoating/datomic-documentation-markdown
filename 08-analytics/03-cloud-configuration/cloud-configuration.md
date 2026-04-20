# Configuration

Datomic analytics is implemented via a Datomic connector to [Trino](https://trino.io/blog/2020/12/27/announcing-trino.html). To begin using analytics, perform the following steps:

| Step | When |
|------|------|
| [Prerequisites](#prerequisites) | All Datomic analytics use |
| [Install Datomic analytics](#install-datomic-analytics) | One-time setup |
| [Configure Trino](#configure-a-trino-cluster) | One-time setup |
| [Configure a Datomic connection](#configure-connections) | Per Datomic system/db |
| [Configure a Datomic metaschema](#configure-metaschema) | Per Datomic Metaschema |
| [Start and stop analytics](#start-and-stop-trino) | As desired |

The Datomic analytics distribution includes sample configuration and templates so that you can complete these steps in minutes. Each of these steps is explained below, along with pointers for deploying analytics [for production use](#trino-in-production).

## Prerequisites

- An accessible [Datomic Cloud system](../../05-operation/02-cloud/02-start-a-system/start-a-system.md)
- Java 11

## Install Datomic Analytics

[Download (1.17GB)](https://downloads.datomic.com/builds/datomic-presto-server/datomic-presto-server-96.zip) and unzip the latest version (0.9.96) of the Datomic Presto server. This zip file includes the PrestoSQL distribution (now named Trino, and referred to as Trino from here on), the Datomic connector, and example configuration files for getting started.

## Configure a Trino Cluster

Trino configuration lives under a directory that Trino commands call the `etc-dir`. The `etc-dir` is specified when [launching Trino](#start-and-stop-trino).

The `etc-samples` directory of Datomic analytics is part of the Trino `etc-dir` configured to run a minimal Trino cluster of a single node on your local machine. You can leave these configuration files unchanged while exploring Trino on your local machine, and consult the [Trino docs](https://trino.io/docs/current/) when you are ready to plan a production cluster.

## Configure Connections

For each Datomic system, you have to create a Trino [catalog](https://trino.io/docs/current/overview/concepts.html#catalog) property file in the `catalog` subdirectory of the Trino [etc-dir](#configure-a-trino-cluster). A catalog file has a name of the form `<catalog>.properties`, where `<catalog>` is the catalog name. `/etc-samples/catalog` in the provided zip has an example catalog properties.

A Datomic catalog property file has the following entries:

| Property | Required? | Value | Notes |
|----------|-----------|-------|-------|
| `connector.name` | Yes | `"datomic"` | Do not change |
| `datomic.client.config` | Yes | A Datomic [client connect map](../../04-apis/04-client-api/client-api.md#client-object) | Must be on one line |
| `datomic.databases` | No | A vector of Datomic database names | |

The `datomic.databases` property lists the databases that are available via Trino. Supplying one or more databases enables [JDBC Metadata](#enabling-jdbc-metadata). If this property is omitted, all databases will be available and not automatically queried.

Any time you change a catalog file, you have to stop and then start the Trino cluster for your changes to take effect.

The `etc-samples` directory includes a catalog named `sample.properties`. Before you start Trino, edit this file and set `datomic.client.config` to have a valid [client connect map](../../04-apis/04-client-api/client-api.md#client-object) for your system.

You can also rename the file from `sample.properties` to something more descriptive of your system.

### Enabling JDBC Metadata

Many analytics tools provide the ability to automatically explore all of the tables in your system. Such exploration involves issuing one *(or many)* JDBC metadata queries, forcing each database in your system to be loaded.

Because these automatic queries can be so expensive, they are disabled by default. To enable JDBC metadata queries, explicitly enumerate the Datomic databases you want to query with the `datomic.databases` property [described above](#configure-connections).

## Configure Metaschema

Metaschema files control the mapping between Datomic attributes and SQL tables and columns. Metaschema files are `.edn` files in the `datomic` subdirectory of Trino's `etc-dir`. Metaschema files can have any name you find convenient, and Datomic analytics automatically associate Metaschemas with any database that has matching attributes.

The `etc-samples` directory includes a Metaschema file matching the [mbrainz database](https://github.com/Datomic/mbrainz-importer).

To facilitate interactive development, Datomic analytics automatically discovers changes to Metaschema files within a minute of changes. You do *not* need to restart your Trino cluster to pick up changes to Metaschema files. However, if you use an analytics tool such as Metabase (that scans Trino to discover schema) you may need to re-run the scan manually to pick up Metaschema changes.

## Start and Stop Trino

Datomic analytics can be launched with Trino's `bin/launcher` command.

- First, make scripts executable:

```
chmod +x bin/launcher*
```

- To run analytics in the foreground of a shell window, navigate to the root directory of the Datomic analytics bundle and enter:

```
bin/launcher run --etc-dir=etc-samples
```

- To verify that your Trino cluster is running, go to [localhost:8989](http://localhost:8989), enter the username `admin`, and leave the password field blank. A cluster overview should be displayed with metrics about your cluster.
- To stop Trino, interrupt or kill the process, e.g. with Ctrl-C.

## Trino in Production

The sample configuration that ships with Datomic analytics will have you up and querying in minutes, but it only scratches the surface of things you may want to do. Trino also allows you to join across disparate data sources, and cluster for horizontal scale.

When you are ready to run Trino in production, you will want to:

- [Tune your JVM config](https://trino.io/docs/current/installation/deployment.html?=catalog#jvm-config) to take full advantage of available memory
- Run Trino [as a daemon](https://trino.io/docs/current/installation/deployment.html?=catalog#running-trino) or in a container
- [Configure authn/authz](https://trino.io/docs/current/security/overview.html)

All of these options and more are covered in the [Trino deployment docs](https://trino.io/docs/current/overview.html).

After these steps, it's possible to [use the SQL Client](../04-sql-cli/sql-cli.md).

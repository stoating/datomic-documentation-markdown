# Troubleshooting Analytics Support

## Monitoring Analytics Server

The Presto server provides a live monitoring dashboard that shows running and completed queries. You can access the dashboard by visiting [localhost:8989](http://localhost:8989) in a web browser.

> Note that if you're using Datomic Cloud, your [access gateway proxy](../14-connecting-pro/connecting-pro.md) must be running to access the dashboard.

## Debug CLI

The [SQL CLI](../04-sql-cli/sql-cli.md) is recommended for troubleshooting analytics connection and configuration issues.

Launch the CLI in debug mode:

```
./presto --server <local-ip OR localhost>:8989 --debug
```

## Checking Analytics Server Status

In the [debug CLI](#debug-cli), run the following system queries:

```sql
SHOW SCHEMAS FROM system;

SHOW TABLES FROM system.runtime;
```

If these queries work correctly, your analytics gateway and analytics system are up and functioning correctly.

If you do not see results from these queries, you should [verify your analytics configuration](../02-pro-configuration/pro-configuration.md).

## Troubleshooting Configuration Issues

If you encounter a `schemaName is null` error when using the CLI, you may have a catalog or Metaschema configuration issue.

Run the following query in the [debug CLI](#debug-cli):

```sql
SELECT * FROM system.metadata.catalogs;
```

The results of this query will include all catalogs that are available in the analytics system. [Your catalog](../02-pro-configuration/pro-configuration.md#catalog) will be listed in the results. If not, ensure that you have [created](../02-pro-configuration/pro-configuration.md#catalog) and [synced](../../05-operation/02-cloud/07-cli-tools/cli-tools.md#sync) your catalog properties file correctly. Be sure that you have run sync with the correct directory structure (a `catalog` and a `datomic` directory).

Another possible cause of this error is that your [Metaschema file](../02-pro-configuration/pro-configuration.md#metaschema) is not properly formatted edn or is not correctly located. Also, verify that your Metaschema file ends with a .edn extension.

## Troubleshooting Metaschema

If you can connect using your catalog and schema, but the expected tables are not displayed, you likely have a misconfiguration of your [Metaschema file](../02-pro-configuration/pro-configuration.md#metaschema).

Metaschemas are dynamically associated with Datomic databases. Errors or typos in Metaschemas can prevent association, resulting in the lack of any Presto tables.

- Start the [SQL CLI](../04-sql-cli/sql-cli.md) with your catalog and schema names
- Then run the following:

```sql
SHOW tables;
```

At least two tables: `db__idents` and `db__attrs` will be displayed, which are both automatically generated for any Datomic database.

- You can examine the contents of these tables with:

```sql
SELECT * FROM db__idents;
SELECT * FROM db__attrs;
```

The contents of these tables indicate the attributes and idents that Datomic analytics was able to read from your Datomic database. If you do not see any additional tables, your Metaschema does not match the schema of this Datomic database.

Ensure that all attributes listed in your Metaschema file exist in your Datomic database (or in the two `db__` tables in Presto) and that you have not accidentally mistyped or included an attribute that is not present in your database.

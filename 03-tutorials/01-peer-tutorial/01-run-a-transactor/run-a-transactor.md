# Run a Transactor

## Running a Transactor

For this tutorial, you will run a `dev` mode transactor on your local machine. `Dev` storage will persist your data by using local disk files for [storage](../../../05-operation/01-pro/01-storage-services/storage-services.md). It requires a transactor to be running.

To start a transactor, you need a transactor properties file. Datomic includes example properties files for each storage. Copy the `config/samples/dev-transactor-template.properties` file to a location of your choice, so that you can edit it later if needed.

This guide will use the `config` directory in your datomic-pro distribution directory:

```sh
cp config/samples/dev-transactor-template.properties config/dev-transactor-template.properties
```

## Starting a Transactor

From your shell system, run:

```sh
bin/transactor config/dev-transactor-template.properties
```

```
Launching with Java options -server -Xms1g -Xmx1g -XX:+UseG1GC -XX:MaxGCPauseMillis=50
System started 
```

Next, you will use this database URI to [connect to a database](../02-connect-to-a-database/connect-to-a-database.md). The transactor process will be used for the subsequent steps in this guide. Make sure that it's running while you work through this guide.

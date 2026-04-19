# Pro Setup

This page is for users who have [chosen Datomic Pro](../../introduction.md#datomic-editions) and covers getting Datomic Pro as well as running a transactor.

## Get Datomic Pro

Datomic Pro is distributed as a zip. You can download the latest version of Datomic Pro here or with this curl command:

```sh
curl https://datomic-pro-downloads.s3.amazonaws.com/1.0.7556/datomic-pro-1.0.7556.zip -O
```

After you download Datomic, unzip it locally. Throughout the documentation, shell commands are run from the root directory of the Datomic install. Change to this directory now, e.g.

```
cd /home/user/datomic/datomic-pro-1.0.7556
```

## Run a Transactor

This section is for users who have [downloaded Datomic Pro](#get-datomic-pro) and covers running a transactor. Before running a production transactor, you must [set up a storage service](../../05-operation/01-pro/01-storage-services/storage-services.md) and configure transactor properties. For local development, you can skip these steps and use Datomic's included dev storage. Datomic's dev storage uses an H2 database embedded in the transactor process, with a default configuration that exposes H2 on ports *4334* and *4335*.

### Starting a Transactor

To start a transactor, run `bin/transactor`, passing a transactor properties file. This command shows using the sample dev properties file that is included with Datomic.

```sh
bin/transactor config/samples/dev-transactor-template.properties
```

The transactor is ready once the message "System started" appears on stdout.

### Stopping a Transactor

To stop a transactor, kill the transactor process, e.g. by typing *Ctrl+C* in the shell process where you ran `bin/transactor` or by sending a kill signal, e.g.

```sh
kill $(DATOMIC_PROCESS_ID)
```

Once you are able to start and stop Datomic processes, you can [integrate the peer library](../../02-accessing/01-peer-library/peer-library.md) with your Java or Clojure project and [start using Datomic](../../03-tutorials/01-peer-tutorial/peer-tutorial.md).

## Supported Java Versions

Datomic Pro supports LTS versions of Java, currently 11, 17, and 21.

Changes to Java support over time are recorded in the [Datomic Pro changelog](../../11-releases/01-datomic-pro/02-pro-change-log/pro-change-log.md) and [release notices](../../11-releases/01-datomic-pro/03-pro-release-notices/pro-release-notices.md).

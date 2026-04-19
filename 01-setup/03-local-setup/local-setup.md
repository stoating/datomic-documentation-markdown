# Local Dev and CI with Datomic Local

With Datomic Local you can develop and test applications with minimal connectivity and setup. Get the datomic local library, add it to your classpath, and you have full access to the [client API](../../02-accessing/02-client-library/client-library.md). This allows you to:

- Develop and test Datomic Cloud applications without connecting to a server and [without changing your application code](../../04-apis/05-datomic-local-api/datomic-local-api.md#divert-system)
- Create [small, single-process](#limits) Datomic applications and libraries that can be embedded and redistributed

Datomic Local is available at no cost and is [Apache licensed](https://www.apache.org/licenses/LICENSE-2.0.html).

This document includes everything you need to know to use Datomic Local:

- How to [get and configure](#setup) Datomic Local
- How to [add Datomic Local to your classpath and create a client](#using-datomic-local)
- How to create [durable](#durability) and [in-memory](#memdb) databases
- Operational [limits](#limits)

### Setup

Datomic Local is available on Maven. You can include it in project deps with:

```
com.datomic/local    {:mvn/version "1.0.291"}
```

### Configure Local Storage

By default, datomic stores all databases under a common storage directory. To specify this directory, create a `.datomic/local.edn` file in your home directory, containing a map with `:storage-dir` and an absolute path:

```clojure
{:storage-dir "/full/path/to/where/you/want/data"}
```

## Using Datomic Local

There are two steps to use Datomic Local: add the Datomic Local library to your classpath and create a local client.

To add Datomic Local to your classpath, add a `com.datomic/local` entry to your [deps.edn file](https://clojure.org/guides/deps_and_cli).

```clojure
{com.datomic/local
 {:mvn/version "1.0.291"}}
```

The Datomic Local dependency is all you need for using Datomic Local.

There are two ways to get a local client:

- You can explicitly request a local client
- You can *divert* requests for Datomic Cloud clients to use local storage for development and testing

To explicitly request a local client, pass a map to `d/client` with:

- `:server-type :datomic-local`
- A `:system` name

```clojure
(require '[datomic.client.api :as d])
(def client (d/client {:server-type :datomic-local
                       :system "dev"}))
```

If you are using Datomic Local to develop and test a Datomic Cloud application, [add the client-cloud dependency](../../02-accessing/02-client-library/client-library.md#deps) to your project. Then, to divert an existing Datomic Cloud system to Datomic Local, call [divert-system](../../04-apis/05-datomic-local-api/datomic-local-api.md#divert-system):

```clojure
(require '[datomic.local :as dl])
(dl/divert-system {:system "production"})

;; existing requests for Cloud system will be served locally!
(def client (d/client {:system "production"
                       :server-type :ion
                       :region "us-east-1"
                       :endpoint "https://ljfrt3pr18.execute-api.us-east-1.amazonaws.com"}))
```

You can also use [import-cloud](../../04-apis/05-datomic-local-api/datomic-local-api.md#import-cloud) to import data from Datomic Cloud to local storage.

If you are new to Datomic, you can now work through the [tutorial](../../03-tutorials/02-client-tutorial/client-tutorial.md).

### Sample Data

`datomic-samples` is a set of databases that are used throughout these Datomic docs and the [Day of Datomic](https://github.com/cognitect-labs/day-of-datomic-cloud) tutorials. To install the Datomic samples on your local computer:

- Download the latest [(2020-07-10) datomic-samples.zip](https://datomic-samples.s3.amazonaws.com/datomic-samples-2020-07-07.zip)
- Unzip the datomic-samples zip into your [storage dir](#configure-local-storage)

You can connect and use them by setting your Datomic Local system name to "datomic-samples":

```clojure
(require '[datomic.client.api :as d])
(def client (d/client {:server-type :datomic-local
                       :system "datomic-samples"}))
(d/list-databases client {})
=> ["mbrainz-subset" "solar-system" "social-news" "movies" ...]
```

## Durability

Datomic Local stores data to your local filesystem, in directories under the `:system` you specify when creating a Datomic Local client.

Each database will store transactions in a directory named `<storage-dir>/<system-name>/<database-name>`. You can "backup" or "restore" a Datomic Local database simply by copying the database directory.

## Memdb

Sometimes durable storage is unnecessary and/or inconvenient. For example, a CI system may not need data after the tests run and no access to a file system.

You can force Datomic Local to use a memory-only database by passing `:storage-dir :mem` to the map you pass when creating a client, as shown in the example below:

```clojure
(def client (d/client {:server-type :datomic-local
                       :storage-dir :mem
                       :system "ci"}))
```

## Limits

- Datomic Local is in-process with your application code, and has all the tradeoffs (vs. a server or cluster) that this implies.
- Datomic Local requires 32 bytes of JVM heap per datom. You should plan your application with this in mind, while also leaving memory for your application's use.
- Datomic Local relies on OS page caching for performance, so leave some RAM available (i.e. not allocated to the JVM heap) for this.
- Datomic Local limits the total number of datoms in a transaction to 10^5.
- Datomic Local limits strings to 4096 characters.
- Datomic Local uses shared FileChannels. This is very efficient but is intolerant of Thread.interrupt. If you interrupt Datomic Local I/O, subsequent I/O may trigger a ClosedChannelException. Try to avoid using interrupt as a control mechanism. If you cannot, you can resume by calling `release-db` and reacquiring a connection.

Datomic Local is best suited for small, single-process applications. For larger projects, [create a Datomic Cloud system](../02-cloud-setup/cloud-setup.md).

### Next Steps

If you are trying Datomic Local for the first time, a good next step is the [client API tutorial](../../03-tutorials/02-client-tutorial/client-tutorial.md).

- [Datomic Local API](../../04-apis/05-datomic-local-api/datomic-local-api.md)
- Check the [Datomic Local change log](../../11-releases/03-datomic-local-change-log/datomic-local-change-log.md) for details on each Datomic Local release

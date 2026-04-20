# Connect to a Database

Previously you learned how to [run a transactor](../01-run-a-transactor/run-a-transactor.md). This section guides on how to connect to a database.

## Creating a Database

- To begin, launch a REPL from the root directory of the Datomic folder using the `bin/repl` script:

```clojure
bin/repl
```

- Require the Peer API:

```clojure
(require '[datomic.api :as d])
```

```
nil
```

The Datomic Peer API [names databases](../../../04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md#datomic.api/connect) with a URI that includes the protocol name, storage connection information, and a database name. The complete URI for a database named "hello" on the [transactor you started in the previous step](../01-run-a-transactor/run-a-transactor.md#start) is `"datomic:dev://localhost:4334/hello"`.

- Def a var, `db-uri`, with this name:

```clojure
(def db-uri "datomic:dev://localhost:4334/hello")
```

```
#'user/db-uri
```

- Create the "hello" database using the `create-database` function:

```clojure
(d/create-database db-uri)
```

```
true
```

## Create a Connection

Once you have [created a database](#create-db), you can use the Datomic peer library to interact with it.

- Connect to the transactor in the same REPL that you used to create your database:

```clojure
(def conn (d/connect db-uri))
```

```
#'user/conn
```

- You will see that a var was created called `conn` which is holding your database connection. To inspect it, run:

```clojure
conn
```

```
#object[datomic.peer.Connection
        0x10a59519
        "{:unsent-updates-queue 0, :pending-txes 0, :next-t 1005, :basis-t 1001, :index-rev 0, :db-id \"hello-c33c1487-877a-404a-88d0-0aac99518598\"}"]
```

Any transactions submitted to the connection will be persisted in the storage that you chose when creating your database.

- Try adding a new entity with the `:db/doc` value "Hello world":

```clojure
@(d/transact conn [{:db/doc "Hello world"}])
```

```
{:db-before datomic.db.Db@e438f2c9,
 :db-after datomic.db.Db@5d0a1343,
 :tx-data [#datom[13194139534317 50 #inst"2024-05-13T00:52:21.776-00:00" 13194139534317 true]
           #datom[17592186045422 62 "Hello world" 13194139534317 true]],
 :tempids {-9223300668110598143 17592186045422}}
```

## Interact with Datomic

The next step will be to define some [schema for your new database](../03-transact-schema/transact-schema.md).

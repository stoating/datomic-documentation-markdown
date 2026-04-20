# Class Peer

**Package:** [datomic](../../peer-api-javadoc.md)

**Inheritance:** [java.lang.Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) > datomic.Peer

`public class Peer extends Object`

Main entry point, used to manage connections, submit transactions, and query.

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| `static Object` | [`administerSystem(Map options)`](#administersystem) | Administer a Datomic system. |
| `static void` | [`cancel(Object anomaly)`](#cancel) | Cancels the current Datomic operation (query or transaction). |
| `static Connection` | [`connect(Object uriOrMap)`](#connect) | Connects to the specified database. |
| `static boolean` | [`createDatabase(Object uriOrMap)`](#createdatabase) | Creates a database with the given name. |
| `static boolean` | [`deleteDatabase(Object uriOrMap)`](#deletedatabase) | Deletes a database. |
| `static datomic.functions.Fn` | [`function(Map m)`](#function) | Generates a function object given a map of `:lang`, `:params`, and `:code`. |
| `static List<String>` | [`getDatabaseNames(Object uriOrMap)`](#getdatabasenames) | Returns a list of database names. |
| `static Object` | [`part(Object entityId)`](#part) | Returns the partition of this entity id. |
| `static Collection<List<Object>>` | [`q(Object query, Object... inputs)`](#q) | Like `query(Object, Object...)`, but with a more specific return signature. |
| `static Stream<Object>` | [`qseq(Object query, Object... inputs)`](#qseq) | Performs the query, deferring item transformations until the Stream is consumed. |
| `static <T> T` | [`query(Object query, Object... inputs)`](#query) | Executes a [datalog query](../../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md). |
| `static <T> T` | [`query(QueryRequest queryRequest)`](#query-queryrequest) | Like `query(Object, Object...)`, but accepts a [`QueryRequest`](../query-request/query-request.md) object. |
| `static boolean` | [`renameDatabase(Object uriOrMap, String newName)`](#renamedatabase) | Renames a database. |
| `static Object` | [`resolveTempid(Database db, Object tempids, Object tempid)`](#resolvetempid) | Resolve a tempid to the actual id assigned in a database. |
| `static void` | [`shutdown(boolean shutdownClojure)`](#shutdown) | Shutdown all peer resources. |
| `static UUID` | [`squuid()`](#squuid) | Constructs a semi-sequential UUID. |
| `static long` | [`squuidTimeMillis(UUID squuid)`](#squuidtimemillis) | Get the time component of a squuid. |
| `static Object` | [`tempid(Object partition)`](#tempid) | Generates a temp id in the designated partition. |
| `static Object` | [`tempid(Object partition, long idNumber)`](#tempid) | Generates a temp id in the designated partition with a specific id number. |
| `static long` | [`toT(Object tx)`](#tot) | Returns the t value associated with this tx. |
| `static Object` | [`toTx(long t)`](#totx) | Returns the tx associated with this t value. |

**Methods inherited from class java.lang.Object:** [clone](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#clone()), [equals](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object)), [finalize](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#finalize()), [getClass](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#getClass()), [hashCode](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#hashCode()), [notify](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#notify()), [notifyAll](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#notifyAll()), [toString](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#toString()), [wait](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait())

## Method Details

### connect

`public static Connection connect(Object uriOrMap)`

Connects to the specified database. The systemId and credentials are defined when a new Datomic instance is provisioned.

URI syntax:

```
;; DynamoDB using roles
datomic:ddb://{aws-region}/{dynamodb-table}/{db-name}

;; DynamoDB using keys (use roles if possible)
datomic:ddb://{aws-region}/{dynamodb-table}/{db-name}?aws_access_key_id={XXX}&aws_secret_key={YYY}

;; DynamoDB Local
datomic:ddb-local://{endpoint}:{port}/{dynamodb-table}/{db-name}?aws_access_key_id={XXX}&aws_secret_key={YYY}

;; Couchbase
datomic:couchbase://{host}/{bucket}/{dbname}[?password={xxx}]

;; SQL
datomic:sql://{db-name}?{jdbc-url}

;; Infinispan
datomic:inf://{cluster-member-host}:{port}/{db-name}

;; Cassandra
datomic:cass://{cluster-member-host}[:{port}]/{keyspace}.{table}/{db-name}[?user={user}&password={pwd}][&ssl=true]

;; Dev Appliance
datomic:dev://{transactor-host}:{port}/{db-name}[?password={password}]

;; Limited-edition transactor integrated storage
datomic:limited-edition://{transactor-host}:{port}/{db-name}[?password={password}]

;; In-process memory
datomic:mem://{db-name}
```

Note that URIs must be percent encoded and db-name cannot contain the following characters: `/ " * : = ?`

The dev, free, and limited-edition protocols use an additional port to communicate with storage. By default this port is one higher than the specified transactor port. You can override the default by specifying `h2-port` in the query string, e.g.

```
datomic:limited-edition://localhost:4334/mydb?h2-port=6000
```

The sql protocol also supports a map format for the connection params:

```clojure
{:protocol :sql
 :db-name "myDb"

 :data-source aDataSourceObject
 ;; OR
 :factory aCallableReturningConnection}
```

Note only one of `:data-source` or `:factory` should be supplied. The keys and protocol name can be keywords or strings.

The cass protocol also supports a map format for the connection params:

```clojure
{:protocol :cass
 :db-name "myDb"
 :table "myKeyspace.myTable"
 :cluster aClusterObject}
```

Note `aClusterObject` must be an instance of type `com.datastax.driver.core.Cluster`. The keys and protocol name can be keywords or strings.

Datomic connections do not adhere to an acquire/use/release pattern. They are thread-safe and long lived. Connections are cached such that calling `Peer.connect(uri)` multiple times with the same URI value will return the same connection object.

**Parameters:** `uriOrMap` — a Datomic connection URI string, or parameters map if supported by the protocol

**Returns:** a connection to the database

---

### createDatabase

`public static boolean createDatabase(Object uriOrMap)`

Creates a database with the given name. The systemId and credentials are defined when a new Datomic instance is provisioned. Idempotent.

**Parameters:** `uriOrMap` — the URI of the database to create

**Returns:** `true` if the database was created, `false` if the database already exists

---

### renameDatabase

`public static boolean renameDatabase(Object uriOrMap, String newName)`

Renames a database.

**Parameters:**
- `uriOrMap` — same as in [`connect(Object)`](#connect)
- `newName` — the new name of the database (database name only, not a URI)

**Returns:** `true` if rename succeeded

---

### deleteDatabase

`public static boolean deleteDatabase(Object uriOrMap)`

Deletes a database.

**Parameters:** `uriOrMap` — same as in [`connect(Object)`](#connect)

**Returns:** `true` if delete occurred

---

### getDatabaseNames

`public static List<String> getDatabaseNames(Object uriOrMap)`

Returns a list of database names.

URI is a database URI as described in the [`connect(Object)`](#connect) documentation, but with a `*` where the database name would be. For instance:

```
datomic:dev://{transactor-host}:{port}/*
```

When using the map form, `:db-name` should be omitted.

**Parameters:** `uriOrMap` — same as in `connect(Object)`, with a `*` where the database name would be, or omitted in the map form

**Returns:** list of database names

*Since: 0.9.5040*

---

### administerSystem

`public static Object administerSystem(Map options)`

Administer a Datomic system. Throws if operation fails.

**Parameters:** `options` — Map

**Returns:** data describing the operation result

*Since: 0.9.5893*

---

### squuid

`public static UUID squuid()`

Constructs a semi-sequential UUID. Useful for creating UUIDs that don't fragment indexes.

**Returns:** a UUID whose most significant 32 bits are currentTimeMillis rounded to seconds

*Since: 0.8.3343*

---

### squuidTimeMillis

`public static long squuidTimeMillis(UUID squuid)`

Get the time component of a squuid.

**Parameters:** `squuid` — a UUID created by `squuid()`

**Returns:** the time in the format of `System.currentTimeMillis`

*Since: 0.8.3343*

---

### tempid

`public static Object tempid(Object partition)`

`public static Object tempid(Object partition, long idNumber)`

Generates a temp id in the designated partition. Within the scope of a single transaction, tempids map consistently to permanent ids.

For the two-argument form, values of `idNumber` from -1 (inclusive) to -1000000 (exclusive) are reserved for user-created temp ids.

**Parameters:**
- `partition` — a keyword identifying the partition
- `idNumber` — a long in the range (-1000000, -1]

**Returns:** a temp id

---

### toT

`public static long toT(Object tx)`

Returns the t value associated with this tx.

**Parameters:** `tx` — a tx

**Returns:** a t value

*Since: 0.8.3372*

---

### toTx

`public static Object toTx(long t)`

Returns the tx associated with this t value.

**Parameters:** `t` — a t value

**Returns:** a tx

*Since: 0.8.3372*

---

### part

`public static Object part(Object entityId)`

Returns the partition of this [entity id](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entities).

**Parameters:** `entityId` — an entity id

**Returns:** a partition

*Since: 0.8.3372*

---

### q

`public static Collection<List<Object>> q(Object query, Object... inputs)`

Like [`query(Object, Object...)`](#query), but with a more specific return signature. Supports the same [grammar](../../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#grammar) as `query`, except for `find-coll`, `find-scalar`, and `find-tuple`.

---

### query

`public static <T> T query(Object query, Object... inputs)`

Executes a [datalog query](../../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md).

In addition to basic pattern matching, query supports:

- joins
- rules
- arbitrary predicates and functions
- aggregates
- binding options for inputs
- find specification for outputs
- pull patterns

Query can be applied to multiple inputs, including both databases and plain old data. See the [grammar](../../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#grammar) and [documentation](../../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#queries) for complete details.

Query runs locally in a Peer process. Query is set-based: intermediate and final result sets must fit in memory.

The `query` data structure passed in can be one of:

- a map that may include `:find`, `:with`, `:in`, and `:where` keys
- a list representation of that same map
- a serialized [edn](https://github.com/edn-format/edn) string of the list or map form

The `inputs` can be any of:

- database values
- arbitrary values
- rules
- pull patterns

If a single database value is provided as input, then no `:in` section is required in the `query`.

**Type Parameters:** `T` — a type compatible with the [find specification](../../../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#find-specifications)

**Parameters:**
- `query` — a data structure describing the query
- `inputs` — inputs bound to the names in the `:in` section of `query`

**Returns:** a data structure based on the find specification

*Since: 0.9.5040*

---

### query(QueryRequest) {#query-queryrequest}

`public static <T> T query(QueryRequest queryRequest)`

Like [`query(Object, Object...)`](#query), but accepts a [`QueryRequest`](../query-request/query-request.md) object.

*Since: 0.9.5153*

---

### qseq

`public static Stream<Object> qseq(Object query, Object... inputs)`

Performs the query described by `query` and `inputs` (as per [`query(Object, Object...)`](#query)). Item transformations such as pull are deferred until the Stream is consumed. For queries with pull(s), this results in:

- reduced memory use and the ability to execute larger queries
- lower latency before the first results are returned

**Parameters:**
- `query` — a data structure describing the query
- `inputs` — inputs bound to the names in the `:in` section of `query`

**Returns:** a [`Stream`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/Stream.html) of the data structure based on the find specification

*Since: 0.9.6110*

---

### function

`public static datomic.functions.Fn function(Map m)`

Generates a function object given a map with the following keys:

- `:lang` — `clojure` or `java`
- `:params` — a list of parameter names used in the code
- `:code` — a string containing the code of the function body (the body of a method corresponding to the params, which will be Objects; can begin with one or more import statements)

**Parameters:** `m` — a map defining the function

**Returns:** a function object implementing `Fn` plus one of `FnN` corresponding to its arity

---

### resolveTempid

`public static Object resolveTempid(Database db, Object tempids, Object tempid)`

Resolve a tempid to the actual id assigned in a database.

**Parameters:**
- `db` — a database
- `tempids` — the `Connection.TEMPIDS` member of a map returned from [`Connection.transact(List)`](../../interfaces/connection/connection.md#transact) or `Connection.transactAsync`
- `tempid` — a tempid

**Returns:** the actual id corresponding to tempid

---

### shutdown

`public static void shutdown(boolean shutdownClojure)`

Shutdown all peer resources. This method should be called as part of clean shutdown of a JVM process. Will release all Connections, and, if `shutdownClojure` is true, will release Clojure resources. Programs written in Clojure can set `shutdownClojure` to false if they manage Clojure resources (e.g. agents) outside of Datomic; programs written in other JVM languages should typically set `shutdownClojure` to true.

**Parameters:** `shutdownClojure` — set true to shutdown Clojure resources

*Since: 0.8.3861*

---

### cancel

`public static void cancel(Object anomaly)`

Cancels the current Datomic operation (query or transaction).

Throws an ex-info with an anomaly to the original caller.

`anomaly` is as described by <https://github.com/cognitect-labs/anomalies>.

`:cognitect.anomalies/category` is a required key, valid values are:

- `:cognitect.anomalies/incorrect`
- `:cognitect.anomalies/conflict`

When `:cognitect.anomalies/message` is provided, the message will be used as the Exception's detail message.

Other keys should be namespace-qualified.

All data passed to cancel must be fressian-serializable.

**Parameters:** `anomaly` — Map

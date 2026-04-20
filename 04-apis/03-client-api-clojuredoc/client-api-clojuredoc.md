# datomic.client.api

Synchronous client library for interacting with Datomic.

This namespace is a wrapper for `datomic.client.api.async`.

Functions in this namespace that communicate with a separate process take an arg-map with the following optional keys:

- `:timeout` — timeout in msec

Functions that support offset and limit take the following additional optional keys:

- `:offset` — number of results to omit from the beginning of the returned data
- `:limit` — maximum total number of results to return; specify -1 for no limit; defaults to -1 for `q` and to 1000 for all other APIs

Functions that return datoms return values of a type that supports indexed (`count`/`nth`) access of `[e a v t added]` as well as lookup (keyword) access via `:e` `:a` `:v` `:t` `:added`.

All errors are reported via ex-info exceptions, with map contents as specified by cognitect.anomalies. See <https://github.com/cognitect-labs/anomalies>.

## Public Variables and Functions

---

## administer-system

**Usage:** `(administer-system client arg-map)`

Run `:action` on system.

Currently the only supported action is:

`:upgrade-schema` — upgrade an existing database to use the latest base schema

`:upgrade-schema` takes the following map:

- `:db-name` — database name

Returns a diagnostic value on success, throws on failure.

---

## as-of

**Usage:** `(as-of db time-point)`

Returns the value of the database as of some time-point.

See [time filters](../../06-reference/06-time-in-datomic/time-in-datomic.md).

---

## client

**Usage:** `(client arg-map)`

Create a client for a Datomic system. This function does not communicate with a server and returns immediately.

For a cloud system, arg-map requires:

- `:server-type` — `:cloud`
- `:region` — AWS region, e.g. `"us-east-1"`
- `:system` — your system name
- `:endpoint` — IP address of your system or query group

Optionally, a cloud system arg-map accepts:

- `:creds-provider` — instance of `com.amazonaws.auth.AWSCredentialsProvider`; defaults to `DefaultAWSCredentialsProviderChain`
- `:creds-profile` — name of an AWS Named Profile; see <http://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html>
- `:proxy-port` — local port for SSH tunnel to bastion

Note: `:creds-provider` and `:creds-profile` are mutually exclusive; providing both will result in an error.

For a datomic-local system, arg-map comprises:

- `:server-type` — `:datomic-local` (required)
- `:system` — a system name (required)
- `:storage-dir` — optional; overrides `:storage-dir` in `~/.datomic/datomic-local.edn`

datomic-local stores databases under `${storage-dir}/${system}/${db-name}`.

For a peer-server system, arg-map requires:

- `:server-type` — `:peer-server`
- `:access-key` — access-key from peer server launch
- `:secret` — secret from peer server launch
- `:endpoint` — peer server host:port
- `:validate-hostnames` — `false`

Returns a client object.

---

## connect

**Usage:** `(connect client arg-map)`

Connects to a database. Takes a client object and an arg-map with keys:

- `:db-name` — database name

Returns a connection. See namespace doc for error and timeout handling.

Returned connection supports `ILookup` for key-based access. Supported keys are:

- `:db-name` — database name

---

## create-database

**Usage:** `(create-database client arg-map)`

Creates a database specified by arg-map with key:

- `:db-name` — the database name

Returns true. See namespace doc for error and timeout handling.

NOTE: `create-database` is not available with peer-server. Use a Datomic Peer to create databases with Datomic On-Prem.

---

## datoms

**Usage:** `(datoms db arg-map)`

Returns an Iterable of datoms from an index as specified by arg-map:

- `:index` — one of `:eavt`, `:aevt`, `:avet`, or `:vaet`
- `:components` — optional vector in the same order as the index containing one or more values to further narrow the result

Datoms are associative and indexed:

| Key      | Index | Value               |
|----------|-------|---------------------|
| `:e`     | 0     | entity id           |
| `:a`     | 1     | attribute id        |
| `:v`     | 2     | value               |
| `:tx`    | 3     | transaction id      |
| `:added` | 4     | boolean add/retract |

For a description of Datomic indexes, see [index APIs](../07-index-apis/index-apis.md).

See namespace doc for timeout, offset/limit, and error handling.

---

## db

**Usage:** `(db conn)`

Returns the current database value for a connection.

Supports `ILookup` interface for key-based access. Supported keys are:

- `:db-name` — database name
- `:t` — basis t for the database
- `:as-of` — a point in time
- `:since` — a point in time
- `:history` — true for history databases

---

## db-stats

**Usage:** `(db-stats db)`

Queries for database stats. Returns a map including at least:

- `:datoms` — total count of datoms in the (history) database

See namespace doc for timeout and error handling.

---

## delete-database

**Usage:** `(delete-database client arg-map)`

Deletes a database specified by arg-map with keys:

- `:db-name` — the database name

Returns true. See namespace doc for error and timeout handling.

NOTE: `delete-database` is not available with peer-server. Use a Datomic Peer to delete databases with Datomic On-Prem.

---

## history

**Usage:** `(history db)`

Returns a database value containing all assertions and retractions across time. A history database can be passed to `datoms`, `index-range`, and `q`, but not to `with` or `pull`. Note that queries against a history database will include retractions as well as assertions. Retractions can be identified by the fifth datom field `:added`, which is true for asserts and false for retracts.

See [time filters](../../06-reference/06-time-in-datomic/time-in-datomic.md).

---

## index-pull

**Usage:** `(index-pull db arg-map)`

Walks an index, pulling entities using the selector, returning a lazy seq on the results.

- `:index` — `:avet` or `:aevt`; entities are pulled from the third component (E for `:avet`, or V for `:aevt`)
- `:selector` — a pull selector (see `pull`)
- `:start` — a vector in the same order as the index indicating the initial position; at least `:a` must be specified; iteration is limited to datoms matching `:a`
- `:reverse` — optional; when true, iterate the index in reverse order

When pulling Vs, the attribute specified in `:start` must be `:db.type/ref`.

If the relationship between E and V is many-to-many, you must specify the second component under `:start` to prevent returning duplicates. Datomic will enforce this requirement when possible, i.e. for `:avet`.

See namespace doc for timeout, offset/limit, and error handling.

---

## index-range

**Usage:** `(index-range db arg-map)`

Returns datoms from the AVET index as specified by arg-map:

- `:attrid` — an attribute entity identifier
- `:start` — optional; the start value, inclusive, of the requested range, defaulting to the beginning of the index
- `:end` — optional; the end value, exclusive, of the requested range, defaulting to the end of the index

For a description of Datomic indexes, see [index APIs](../07-index-apis/index-apis.md).

Returns an Iterable of datoms. See namespace doc for offset/limit, timeout, and error handling.

---

## list-databases

**Usage:** `(list-databases client arg-map)`

Lists all databases. arg-map requires no keys but can contain any of the optional keys listed in the namespace doc.

Returns collection of database names.

---

## pull

**Usage:** `(pull db arg-map)`  
**Usage:** `(pull db selector eid)`

Returns a hierarchical selection described by selector and eid.

- `:selector` — the selector expression
- `:eid` — entity id

For a complete description of the selector syntax, see [pull](../../06-reference/03-query-and-pull/03-pull/pull.md).

Returns a map.

The arity-2 version takes `:selector` and `:eid` in arg-map, which also supports `:timeout`. See namespace doc.

You can get information about the I/O reads performed by a pull with io-stats. To request io-stats, add `:io-context` (a qualified keyword) to the arg-map you use to call `pull`. The return value will be a map with:

- `:ret` — the result of the pull
- `:io-stats` — io-stats for the pull

The io-stats map includes:

- `:io-context` — the io-context passed in
- `:api` — `:tx-with`
- `:api-ms` — msec to perform the API call
- `:reads` — breakout of reads by cache tier and index sort
- `:nested` — breakout iff nested queries set `:io-context`

See [io-stats](../10-io-stats/io-stats.md).

---

## q

**Usage:** `(q arg-map)`  
**Usage:** `(q query & args)`

Performs the query described by query and args:

- `:query` — the query to perform: a map, list, or string
- `:args` — data sources for the query, e.g. database values retrieved from a call to `db`, and/or rules

The query list form is:

```clojure
[:find  ?var1 ?var2 ...
 :with  ?var3 ...
 :in    $src1 $src2 ...
 :where clause1 clause2 ...]
```

- `:find` — specifies the tuples to be returned
- `:with` — optional; names vars to be kept in the aggregation set but not returned
- `:in` — optional; omitting `:in ...` is the same as specifying `:in $`
- `:where` — limits the result returned

For a complete description of the query syntax, see [query data reference](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md).

Returns a collection of tuples.

The arity-1 version takes `:query` and `:args` in arg-map, which allows additional options for `:offset`, `:limit`, and `:timeout`. See namespace doc.

You can get information about the I/O reads performed by a query with io-stats. To request io-stats, add `:io-context` (a qualified keyword) to the arg-map. Query will return a map with:

- `:ret` — the result of the query
- `:io-stats` — io-stats for the query

The io-stats map includes:

- `:io-context` — the io-context passed in
- `:api` — `:tx-with`
- `:api-ms` — msec to perform the API call
- `:reads` — breakout of reads by cache tier and index sort
- `:nested` — breakout iff nested queries set `:io-context`

See [io-stats](../10-io-stats/io-stats.md).

You can request query-stats by adding `:query-stats true` to the map you use to call `query`. Query will return a map with:

- `:ret` — the result of the query
- `:query-stats` — query-stats for the query

See [query-stats](../11-query-stats/query-stats.md).

---

## qseq

**Usage:** `(qseq arg-map)`  
**Usage:** `(qseq query & args)`

Performs the query described by query and args (as per `q`), returning a lazy seq on the results. Item transformations such as `pull` are deferred until the seq is consumed. For queries with pull(s), this results in:

- reduced memory use and the ability to execute larger queries
- lower latency before the first results are returned

The returned seq object efficiently supports `count`.

---

## since

**Usage:** `(since db t)`

Returns the value of the database since some time-point.

See [time filters](../../06-reference/06-time-in-datomic/time-in-datomic.md).

---

## sync

**Usage:** `(sync conn t)`

Used to coordinate with other clients. Returns a database value with basis `:t >= t`. Does not make a remote call.

---

## transact

**Usage:** `(transact conn arg-map)`

Submits a transaction specified by arg-map:

- `:tx-data` — a collection of list forms or map forms

For a complete specification of the tx-data format, see [transaction data reference](../../06-reference/02-transactions/02-transaction-data/transaction-data.md).

Returns a map with the following keys:

- `:db-before` — database value before the transaction
- `:db-after` — database value after the transaction
- `:tx-data` — collection of datoms produced by the transaction
- `:tempids` — a map from tempids to their resolved entity IDs

You can get information about the I/O reads performed by a transaction with io-stats. To request io-stats, add `:io-context` (a qualified keyword) to the arg-map. The map returned by `transact` will include an additional `:io-stats` key whose value is a map with:

- `:io-context` — the io-context passed in
- `:api` — `:tx-with`
- `:api-ms` — msec to perform the API call (does not include queue, storage, or network latency)
- `:reads` — breakout of reads by cache tier and index sort
- `:nested` — breakout iff nested queries set `:io-context`

Datomic logs the io-stats for every transaction. If you pass an `:io-context` to `transact`, you can use this context to correlate transactions with I/O costs after the fact.

See [io-stats](../10-io-stats/io-stats.md).

See namespace doc for timeout and error handling.

---

## tx-range

**Usage:** `(tx-range conn arg-map)`

Retrieve a range of transactions in the log as specified by arg-map:

- `:start` — optional; the start time-point, or nil to start from the beginning of the transaction log
- `:end` — optional; the end time-point, exclusive, or nil to run to the end of the transaction log

Returns an Iterable of transactions. Transactions have keys:

- `:t` — the basis t of the transaction
- `:data` — a collection of the datoms in the transaction

See `datoms` for a description of `:data` value.

See namespace doc for offset/limit, timeout, and error handling.

---

## with

**Usage:** `(with db arg-map)`

Applies tx-data to a database returned from `with-db` or a prior call to `with`. The result of calling `with` is a database value as-if the data was applied in a transaction, but the durable database is unaffected.

Takes and returns data in the same format expected by `transact`.

See namespace doc for timeout and error handling.

---

## with-db

**Usage:** `(with-db conn)`

Returns a with-db value suitable for passing to `with`.

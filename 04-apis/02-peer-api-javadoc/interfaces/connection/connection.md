**Package** [datomic](../../peer-api-javadoc.md)

# Interface Connection

`public interface Connection`

A connection to a database for submitting and monitoring transactions, and retrieving the current value of the database.

## Field Summary

| Modifier and Type | Field | Description |
|---|---|---|
| `static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`DB_AFTER`](#db_after) | |
| `static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`DB_BEFORE`](#db_before) | |
| `static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`TEMPIDS`](#tempids) | |
| `static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`TX_DATA`](#tx_data) | |

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| [`Database`](../database/database.md) | [`db()`](#db) | Retrieves the current database value. |
| `void` | [`gcStorage(Date olderThan)`](#gcstorage) | Reclaim storage garbage older than a certain age. |
| [`Log`](../log/log.md) | [`log()`](#log) | Retrieves the current value of the log. |
| `void` | [`release()`](#release) | Request the release of resources associated with this connection. |
| `void` | [`removeTxReportQueue()`](#removetxreportqueue) | Removes the queue associated with this connection. |
| `boolean` | [`requestIndex()`](#requestindex) | Request that a [background indexing job](../../../../05-operation/01-pro/04-capacity-planning/capacity-planning.md#indexing) begin immediately. |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` | [`sync()`](#sync) | Retrieve a database value that includes all transactions completed at the time `sync` was called. |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` | [`sync(long t)`](#synclong) | Retrieve a database value that includes all transactions completed up to and including time t. |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` | [`syncExcise(long t)`](#syncexcise) | Retrieve a database value that is aware of all [excisions](../../../../05-operation/01-pro/14-excision/excision.md) up to a [database t](../../../../12-glossary/glossary.md#t). |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` | [`syncIndex(long t)`](#syncindex) | Retrieve a database value that is [indexed](../../../../05-operation/01-pro/04-capacity-planning/capacity-planning.md#indexing) through the [database t](../../../../12-glossary/glossary.md#t) passed in. |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` | [`syncSchema(long t)`](#syncschema) | Retrieve a database value that is aware of all [schema changes](../../../../06-reference/01-schema/02-changing-schema/changing-schema.md#changing-schema) up to a [database t](../../../../12-glossary/glossary.md#t). |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<Map>` | [`transact(List txData)`](#transact) | Submits a [transaction](../../../../06-reference/02-transactions/transactions.md), blocking until a result is available. |
| [`ListenableFuture`](../listenable-future/listenable-future.md)`<Map>` | [`transactAsync(List txData)`](#transactasync) | Like [`transact(List)`](#transact), but returns immediately, with timeout logic left up to the caller. |
| [`BlockingQueue`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/BlockingQueue.html)`<Map>` | [`txReportQueue()`](#txreportqueue) | Gets the single transaction report queue associated with this connection, creating it if necessary. |

## Field Details

### DB_BEFORE

`static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `DB_BEFORE`

### DB_AFTER

`static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `DB_AFTER`

### TX_DATA

`static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `TX_DATA`

### TEMPIDS

`static final` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `TEMPIDS`

## Method Details

### requestIndex

`boolean requestIndex()`

Request that a [background indexing job](../../../../05-operation/01-pro/04-capacity-planning/capacity-planning.md#indexing) begin immediately.

Background indexing will happen asynchronously. You can track indexing completion with [`syncIndex(long)`](#syncindex).

**Returns:** true if indexing job successfully scheduled.

### db

[`Database`](../database/database.md) `db()`

Retrieves the current database value. Does not communicate with the transactor, nor block.

**Returns:** an immutable database value.

### log

[`Log`](../log/log.md) `log()`

Retrieves the current value of the log. Does not communicate with the transactor, nor block.

**Returns:** an immutable log value

**Since:** 0.8.4122

### sync

[`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` `sync()`

Retrieve a database value that includes all transactions completed at the time `sync` was called.

`sync` is a primitive for coordinating activity across peer processes. `sync` always communicates with the transactor, and should only be used when the following two conditions hold:

1. coordination is required
2. peers have no way to agree on a basis t for coordination

If you do not require coordination, prefer [`db()`](#db). If peers can share a known basis t, prefer [`sync(long)`](#synclong).

The future returned by sync can take arbitrarily long to complete. Waiters should specify a timeout.

**Returns:** a database future.

**Since:** 0.8.3993

### sync(long)

[`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` `sync(long t)`

Retrieve a database value that includes all transactions completed up to and including time t.

`sync` is a primitive for coordinating activity across peer processes. `sync` does not communicate with the transactor, but it can block if the peer has not yet been notified of transactions up to time t.

If you do not require coordination, prefer [`db()`](#db). If peers do not share a basis t, prefer [`sync()`](#sync).

The future returned by sync can take arbitrarily long to complete. Waiters should specify a timeout.

**Parameters:**
- `t` — a [database t](../../../../12-glossary/glossary.md#t).

**Returns:** a database future.

**Since:** 0.8.3993

### syncIndex

[`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` `syncIndex(long t)`

Retrieve a database value that is [indexed](../../../../05-operation/01-pro/04-capacity-planning/capacity-planning.md#indexing) through the [database t](../../../../12-glossary/glossary.md#t) passed in.

Does not communicate with the transactor, so the future may be available immediately.

The future can take arbitrarily long to complete. Waiters should specify a timeout.

**Parameters:**
- `t` — a database t.

**Returns:** a database future.

**Since:** 0.9.4470

### syncSchema

[`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` `syncSchema(long t)`

Retrieve a database value that is aware of all [schema changes](../../../../06-reference/01-schema/02-changing-schema/changing-schema.md#changing-schema) up to a [database t](../../../../12-glossary/glossary.md#t).

Does not communicate with the transactor, so the future may be available immediately.

The future can take arbitrarily long to complete. Waiters should specify a timeout.

**Parameters:**
- `t` — a database t.

**Returns:** a database future.

**Since:** 0.9.4470

### syncExcise

[`ListenableFuture`](../listenable-future/listenable-future.md)`<`[`Database`](../database/database.md)`>` `syncExcise(long t)`

Retrieve a database value that is aware of all [excisions](../../../../05-operation/01-pro/14-excision/excision.md) up to a [database t](../../../../12-glossary/glossary.md#t).

Does not communicate with the transactor, so the future may be available immediately.

The future can take arbitrarily long to complete. Waiters should specify a timeout.

**Parameters:**
- `t` — a database t.

**Returns:** a database future.

**Since:** 0.9.4470

### transact

[`ListenableFuture`](../listenable-future/listenable-future.md)`<Map>` `transact(List txData)`

Submits a [transaction](../../../../06-reference/02-transactions/transactions.md), blocking until a result is available.

**Parameters:**
- `txData` — a list of data to be added, containing any combination of [assertions](../../../../06-reference/02-transactions/02-transaction-data/transaction-data.md#assert-and-retract), [retractions](../../../../06-reference/02-transactions/02-transaction-data/transaction-data.md#assert-and-retract), [transaction functions](../../../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md), or [entity maps](../../../../06-reference/02-transactions/02-transaction-data/transaction-data.md#map-forms):

  | Type | Example |
  |---|---|
  | assertion | `[:db/add some-id :some-attr/name "Some attr value"]` |
  | retraction | `[:db/retract some-id :some-attr/name "Some attr value"]` |
  | transaction function | `[:some/fn-name args-for-fn ...]` |
  | entity map | `{:db/id some-id :some-attr/name "some value" :another-attr/name 42 ...}` |

**Returns:** a future that can be used to monitor the completion of the transaction. If the transaction commits, the future's value is a map with the following keys:

  | Key | Value |
  |---|---|
  | `DB_BEFORE` | Database value before the transaction |
  | `DB_AFTER` | Database value after the transaction |
  | `TX_DATA` | Collection of [`Datom`](../datom/datom.md)s produced by the transaction |
  | `TEMPID` | Use with [`Peer.resolveTempid(Database, Object, Object)`](../../classes/peer/peer.md#resolvetempid) to resolve temporary ids. |

  If the transaction aborts, attempts to get the future's value throw an `ExecutionException`, wrapping a `Error` containing error information. If the transaction times out, the call to transact itself will throw a `RuntimeException`. The transaction timeout can be set via the system property `datomic.txTimeoutMsec`, and defaults to 10000 (10 seconds).

**See Also:** [`ListenableFuture`](../listenable-future/listenable-future.md)

### transactAsync

[`ListenableFuture`](../listenable-future/listenable-future.md)`<Map>` `transactAsync(List txData)`

Like [`transact(List)`](#transact), but returns immediately, with timeout logic left up to the caller.

**Parameters:**
- `txData` — see `transact`

**Returns:** see `transact`

### txReportQueue

[`BlockingQueue`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/BlockingQueue.html)`<Map>` `txReportQueue()`

Gets the single transaction report queue associated with this connection, creating it if necessary.

The transaction report queue receives reports from all transactions in the system. Objects on the queue have the same keys as returned by [`transact(List)`](#transact). The returned queue may be consumed from more than one thread. Note that the returned queue does not block producers, and will consume memory until you consume the elements from it. Reports will be added to the queue at some point after the db has been updated. If this connection originated the transaction, the transaction future will be notified first, before a report is placed on the queue.

**Returns:** a queue

### removeTxReportQueue

`void removeTxReportQueue()`

Removes the queue associated with this connection.

### gcStorage

`void gcStorage(Date olderThan)`

Reclaim storage garbage older than a certain age.

As part of [capacity planning](../../../../05-operation/01-pro/04-capacity-planning/capacity-planning.md#garbage-collection-for-live-databases) for a Datomic system, you should schedule regular (e.g. daily, weekly) calls to `gcStorage`.

**Parameters:**
- `olderThan` — limits how recent garbage may be collected

### release

`void release()`

Request the release of resources associated with this connection. Method returns immediately, resources will be released asynchronously. This method should only be called when the entire process is no longer interested in the connection. Note that Datomic connections do not adhere to an acquire/use/release pattern. They are thread-safe, cached, and long lived. Many processes (e.g. application servers) will never call release.

**Since:** 0.8.3861

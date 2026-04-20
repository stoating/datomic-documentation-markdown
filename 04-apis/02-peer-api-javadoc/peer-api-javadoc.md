# Package datomic

`package datomic`

The Datomic peer library is designed to be embedded in application servers. It is the gateway to the rest of the database, submitting [transactions](../../06-reference/02-transactions/transactions.md) and receive live notifications from the transactor. It also provides local, in memory access to the database, including [caching](../../05-operation/01-pro/11-running-on-aws/running-on-aws.md) and [query](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md) capability. It contains all the communication components needed for connecting to the transactor and [storage](../../05-operation/01-pro/01-storage-services/storage-services.md) services, as well as Datalog and other facilities for managing your data.

The peer library can act in standalone mode, using an in-memory database as a stand-in for the other components.

## Class Summary

| Class | Description |
|-------|-------------|
| [Attribute](interfaces/attribute/attribute.md) | Programmatic representation of a [schema attribute](../../06-reference/01-schema/01-schema-reference/schema-reference.md#attributes). |
| [Connection](interfaces/connection/connection.md) | A connection to a database for submitting and monitoring transactions, and retrieving the current value of the database. |
| [Database](interfaces/database/database.md) | An immutable, point-in-time database value. |
| [`Database.Predicate<T>`](interfaces/database-predicate/database-predicate.md) | Boolean-valued function for filtering a database. |
| [Datom](interfaces/datom/datom.md) | An immutable, point-in-time fact: `[entity, attribute, value, transaction, added]` |
| [Entity](interfaces/entity/entity.md) | Implements the [Entity API](../../06-reference/07-entities/entities.md) for associative navigation by attribute keys. |
| [`ListenableFuture<T>`](interfaces/listenable-future/listenable-future.md) | A future that supports completion listeners. |
| [Log](interfaces/log/log.md) | Implements the [Log API](../08-log-api/log-api.md). |
| [Peer](classes/peer/peer.md) | Main entry point, used to manage connections, submit transactions, and query. |
| [QueryRequest](classes/query-request/query-request.md) | Container for parameters to `Peer.query(QueryRequest)` |
| [Util](classes/util/util.md) | Utilities for creating and using data structures. |

# Log API

Datomic's database log is a recording of all transaction data in
historic order, organized for efficient access by transaction. Datomic's
[log](#log) is stored as a shallow tree of segments,
where each segment typically contains thousands of datoms. The most
recent data in the log is also maintained in memory on all transactor
and peer processes.

## Log

[Peer API](../01-peer-api-clojuredoc/peer-api-clojuredoc.md#log)

`log` returns an immutable log value.

```clojure
(d/log conn)
=> #datomic.log.LogValue{:db datomic.db.Db@b4f7bc6f, :olookup #object[datomic.cache$lookup_cache$reify__12495 0x21ff3fe3 "datomic.cache$lookup_cache$reify__12495@21ff3fe3"], :root-id #uuid "6643ba96-0cc9-46fd-8390-21b5e6001fd9", :tail #datomic.db.MemLog{:txes []}}
```

Given a log, you can retrieve an iterable over a range in the log with
[tx-range](#tx-range) or use the [log in query](#log-in-query).

## tx-range

[Peer API](../01-peer-api-clojuredoc/peer-api-clojuredoc.md#tx-range) | [Client API](../03-client-api-clojuredoc/client-api-clojuredoc.md#tx-range)

You can access the log via the tx-range API function.

```clojure
;; Peer API
(d/tx-range log 1000 1020)

;; Client API
(d/tx-range conn {:start 1000 :end 1020})
```

The arguments to tx-range are *start-t* (inclusive), and *end-t* (exclusive). Legal
values for these arguments are:

- Txes (transaction entity ids)
- Ts (as e.g. returned from [next-t](../01-peer-api-clojuredoc/peer-api-clojuredoc.md#next-t))
- Transaction instants (java.util.Dates)
- *nil* (to represent the beginning/end of the log)

`tx-range` returns a collection of transactions, each of which contains key/value
pairs:

| Keyword | Value |
|---------|-------|
| :t | The basis t of the transaction |
| :data | a collection of the datoms asserted/retracted by the transaction |

The datoms implement the [datomic.Datom](../02-peer-api-javadoc/interfaces/datom/datom.md)
interface. In Clojure, they act as both sequential and associative collections, and
can be destructured accordingly.

## Log in Query: tx-ids and tx-data

The log API includes two convenience functions that are available
within the query. The *tx-ids* function takes arguments similar to
[tx-range](#tx-range), but returns a collection of transaction entity ids.
You will typically use the collection binding form *[?tx ...]*
to capture the results:

```clojure
[(tx-ids ?log ?t1 ?tx) [?tx ...]]
```

The following example query returns the count of transactions within
the range [t1, t2):

```clojure
(d/q '[:find (count ?tx)
       :in $ ?log ?t1 ?t2
       :where [(tx-ids ?log ?t1 ?t2) [?tx ...]]]
     (d/db conn) (d/log conn) t1 t2)
```

The *tx-data* function returns the datoms associated with a particular
t or tx. You will typically use the relation binding form *[ [?e ?a
?v _ ?op ] ]* to capture the results:

```clojure
[(tx-data ?log ?tx) [[?e ?a ?v _ ?op]]]
```

Note the underscore binding. You should **not** bind the *?tx* position,
as *?tx* is already bound on input to the function.

*tx-data* is the efficient way to get transaction data given t or
tx. A common scenario is to use *tx-data* in combination with
*tx-ids*, to return the datoms associated with a range in time. The
following example query returns all the entities that were modified on
August 1, 2013:

```clojure
(d/q '[:find ?e
       :in $ ?log ?t1 ?t2
       :where [(tx-ids ?log ?t1 ?t2) [?tx ...]]
              [(tx-data ?log ?tx) [[?e]]]]
     (d/db conn) (d/log conn) #inst "2013-08-01" #inst "2013-08-02")
```

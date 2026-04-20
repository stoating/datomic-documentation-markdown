# Error Handling

This page covers error handling in [Datomic APIs](../../introduction.md#datomic-apis) across all [editions](../../introduction.md#datomic-editions).

Error information should be generic and extensible. Datomic accomplishes this by representing errors as error maps with namespaced keywords for keys. When an error occurs in an asynchronous API, Datomic will place an error map on the channel and then close the channel. When an error occurs in a synchronous API, Datomic will throw an exception that implements IExceptionInfo, and applications can recover the error map with [ex-data](https://clojure.github.io/clojure/clojure.core-api.html#clojure.core/ex-data).

Error information should be actionable. Datomic accomplishes this by dividing errors into categories using the [anomalies library](https://github.com/cognitect-labs/anomalies), which includes guidance for which errors are retryable and the approach needed to resolve each category of error.

While these basic mechanisms are simple, there are several points of complexity to consider, explained in more detail below.

## Arbitrary Java Exceptions

Datomic relies on several third-party libraries, each of which has its own conventions for error handling. While Datomic APIs may wrap such exceptions with anomalies, they do not promise to do so in all cases. Some of Datomic's own codebase predates the existence of the facilities described above and may also throw arbitrary exceptions.

Applications must be prepared to handle arbitrary Java exceptions in addition to the information-bearing exceptions described above.

## Wrapped Exceptions

Java exceptions can wrap underlying cause exceptions. Programs can walk this cause chain with e.g. [ex-cause](https://clojure.github.io/clojure/clojure.core-api.html#clojure.core/ex-cause) or [getCause](https://docs.oracle.com/javase/8/docs/api/java/lang/Throwable.html#getCause--). Error handling routines should be prepared to walk cause chains to find the "interesting" exceptions. To make matters more challenging, library releases sometimes introduce or remove layers in the cause chain. Error handlers therefore need to search the chain semantically, rather than e.g. assuming that the third exception in the chain is the interesting one.

As an example, Datomic APIs that return futures such as [transact](../01-peer-api-clojuredoc/peer-api-clojuredoc.md#transact) and [transact-async](../01-peer-api-clojuredoc/peer-api-clojuredoc.md#transact-async) will *always* wrap exceptions in an [ExecutionException](https://docs.oracle.com/javase/6/docs/api/java/util/concurrent/ExecutionException.html) to comply with the contract of [Future.get](https://docs.oracle.com/javase/6/docs/api/java/util/concurrent/Future.html#get()).

## Marshalling Errors

Datomic is a distributed system, and often needs to convey error information from one process to another, e.g. from transactors to peers or from cluster nodes to cluster clients.

Datomic uses [fressian](https://github.com/Datomic/fressian/wiki) and/or [transit](https://github.com/cognitect/transit-format) to marshal all Datomic data, including errors. These libraries handle all Clojure and Java primitive types and collections. In addition, they use Clojure's Throwable->map to convert exceptions and stacktraces into serializable data.

That said, user code can create errors that Datomic needs to marshal. In particular, [transaction functions](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md) can throw arbitrary exceptions. If a transaction function throws an exception that includes unserializable data Datomic will be unable to marshal it and can at best report a generic error. The prime example of unserializable data that surprises users is a Datomic database value itself. While a database value is information, Datomic will not attempt to serialize it inside an error as it can be arbitrarily large. This restriction also applies to Datomic [entities](../../06-reference/07-entities/entities.md), which are a lazy view on potentially the entire database. If you want to convey a map of entity information in an exception, put it in an actual map.

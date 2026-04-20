# Class QueryRequest

**Package:** [datomic](../../peer-api-javadoc.md)

**Inheritance:** [`java.lang.Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) → `datomic.QueryRequest`

`public class QueryRequest extends Object`

Container for parameters to [`Peer.query(QueryRequest)`](../peer/peer.md#query).

**Since:** 0.9.5153

## Field Summary

| Modifier and Type | Field |
|-------------------|-------|
| `public static final Object` | `ARGS` |
| `public static final Object` | `QUERY` |
| `public static final Object` | `TIMEOUT` |

## Method Summary

| Modifier and Type | Method | Description |
|-------------------|--------|-------------|
| `Map` | [`asData()`](#asdata) | |
| `static QueryRequest` | [`create(Object query, Object... inputs)`](#create) | Creates a QueryRequest object. |
| `QueryRequest` | [`timeout(long timeoutMsec)`](#timeout) | The number of milliseconds after which a query may be stopped. |
| `String` | [`toString()`](#tostring) | |

**Methods inherited from `java.lang.Object`:**
[`clone`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#clone()), [`equals`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object)), [`finalize`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#finalize()), [`getClass`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#getClass()), [`hashCode`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#hashCode()), [`notify`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#notify()), [`notifyAll`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#notifyAll()), [`wait`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait()), [`wait(long)`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait(long)), [`wait(long, int)`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait(long,int))

## Field Details

### ARGS

`public static final Object ARGS`

### QUERY

`public static final Object QUERY`

### TIMEOUT

`public static final Object TIMEOUT`

## Method Details

### create

`public static QueryRequest create(Object query, Object... inputs)`

Creates a QueryRequest object.

`query` and `inputs` take the same form as described in [`Peer.query(Object, Object...)`](../peer/peer.md#query).

**Parameters:**
- `query` - a data structure describing the query
- `inputs` - inputs bound to the names in `:in` section of `query`

**Returns:** a QueryRequest object that can be passed to [`Peer.query(QueryRequest)`](../peer/peer.md#query)

### timeout

`public QueryRequest timeout(long timeoutMsec)`

The number of milliseconds after which a query may be stopped.

Note: timeout is approximate, it is meant to protect against long running queries, but is not guaranteed to stop after precisely the duration specified.

**Parameters:**
- `timeoutMsec` - number of milliseconds after which a query may be stopped.

**Returns:** a reference to the updated QueryRequest so methods can be chained together.

### toString

`public String toString()`

**Overrides:** [`toString`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#toString()) in class `Object`

**Returns:** a string representation of the QueryRequest

### asData

`public Map asData()`

**Returns:** a Map representation of the QueryRequest

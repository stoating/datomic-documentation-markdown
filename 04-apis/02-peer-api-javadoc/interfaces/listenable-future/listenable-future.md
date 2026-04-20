# Interface ListenableFuture&lt;T&gt;

**Package:** [datomic](../../peer-api-javadoc.md)

**All Superinterfaces:** [`Future<T>`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html)

`public interface ListenableFuture<T> extends Future<T>`

A future that supports completion listeners.

**Since:** 0.8.3591

## Nested Class Summary

Nested classes/interfaces inherited from interface `java.util.concurrent.Future`:

[`Future.State`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.State.html)

## Method Summary

| Modifier and Type | Method | Description |
|-------------------|--------|-------------|
| `void` | `addListener(Runnable listener, Executor executor)` | Register a listener to run on the given executor. |

**Methods inherited from `java.util.concurrent.Future`:**
[`cancel`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#cancel(boolean)), [`exceptionNow`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#exceptionNow()), [`get`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#get()), [`get(long, TimeUnit)`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#get(long,java.util.concurrent.TimeUnit)), [`isCancelled`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#isCancelled()), [`isDone`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#isDone()), [`resultNow`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#resultNow()), [`state`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/Future.html#state())

## Method Details

### addListener

`void addListener(Runnable listener, Executor executor)`

Register a listener to run on the given executor. The listener will run once and only once, if and when the Future's work is complete. If the future has completed already, the listener will run immediately. Ordering of listeners is not guaranteed.

**Parameters:**
- `listener` - the listener to run
- `executor` - the executor to run the listener

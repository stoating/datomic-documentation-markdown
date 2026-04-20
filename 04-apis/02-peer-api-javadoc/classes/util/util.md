**Package** [datomic](../../peer-api-javadoc.md)

# Class Util

[java.lang.Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) > datomic.Util

`public final class Util extends` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html)

Utilities for creating and using data structures.

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| `static` [List](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html) | [`list(Object... items)`](#list) | Creates an immutable List. |
| `static` [Map](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Map.html) | [`map(Object... keyvals)`](#map) | Creates an immutable Map. |
| `static` [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html) | [`name(Object k)`](#name) | Returns the name part of a keyword or symbol. |
| `static` [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html) | [`namespace(Object k)`](#namespace) | Returns the namespace part of a keyword or symbol. |
| `static` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`read(String source)`](#read) | Reads one item from source, returning it. |
| `static` [List](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html) | [`readAll(Reader reader)`](#readall) | Reads all the data in reader, returning a List. |
| `static` [Stream](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/Stream.html) | [`streamOn(Iterable it)`](#streamon) | Create a stream on an immutable Iterable. |

### Methods inherited from class java.lang.Object

[clone](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#clone()), [equals](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#equals(java.lang.Object)), [finalize](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#finalize()), [getClass](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#getClass()), [hashCode](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#hashCode()), [notify](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#notify()), [notifyAll](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#notifyAll()), [toString](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#toString()), [wait](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait()), [wait](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait(long)), [wait](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#wait(long,int))

## Method Details

### name

`public static` [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html) `name(` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `k)`

Returns the name part of a keyword or symbol.

**Parameters:**
- `k` — a keyword or symbol

**Returns:** the name

### namespace

`public static` [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html) `namespace(` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `k)`

Returns the namespace part of a keyword or symbol.

**Parameters:**
- `k` — a Keyword

**Returns:** the namespace

### list

`public static` [List](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html) `list(` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `... items)`

Creates an immutable List.

**Parameters:**
- `items` — Objects to be included in the List

**Returns:** an unmodifiable List

### map

`public static` [Map](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Map.html) `map(` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `... keyvals)`

Creates an immutable Map.

**Parameters:**
- `keyvals` — pairs to include in the Map, written as key1, value1, key2, value2, and so on

**Returns:** an unmodifiable Map

### read

`public static` [Object](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) `read(` [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html) `source)`

Reads one item from source, returning it.

**Parameters:**
- `source` — an [edn](https://github.com/edn-format/edn) string

**Returns:** the object read

### readAll

`public static` [List](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html) `readAll(` [Reader](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/io/Reader.html) `reader)`

Reads all the data in reader, returning a List. Closes reader.

**Parameters:**
- `reader` — the [edn](https://github.com/edn-format/edn) data to parse.

**Returns:** a List for the parsed data.

### streamOn

`public static` [Stream](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/Stream.html) `streamOn(` [Iterable](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Iterable.html) `it)`

Create a stream on an immutable Iterable.

**Parameters:**
- `it` — an Iterator

**Returns:** a sequential Stream

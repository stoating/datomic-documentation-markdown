# Interface Log

**Package:** [datomic](../../peer-api-javadoc.md)

`public interface Log`

Implements the [Log API](../../../08-log-api/log-api.md).

*Since: 0.8.4122*

## Field Summary

| Modifier and Type | Field |
|---|---|
| `static final Object` | [`DATA`](#data) |
| `static final Object` | [`T`](#t) |

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| [`Iterable<Map>`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Iterable.html) | [`txRange(Object startT, Object endT)`](#txrange) | Returns a range of transactions in log, starting at start, or from beginning if start is null, and ending before end, or through end of log if end is null. |

## Field Details

### T

`static final Object T`

---

### DATA

`static final Object DATA`

## Method Details

### txRange

`Iterable<Map> txRange(Object startT, Object endT)`

Returns a range of transactions in log, starting at start, or from beginning if start is null, and ending before end, or through end of log if end is null.

Each transaction is a map with the following keys:

| Key | Description |
|---|---|
| `T` | the T point of the transaction |
| `DATA` | a Collection of the Datoms asserted/retracted by the transaction |

**Parameters:**
- `startT` — a [time-point](../../../../12-glossary/glossary.md#time-point) or null
- `endT` — a [time-point](../../../../12-glossary/glossary.md#time-point) or null

**Returns:** an Iterable of transaction maps occurring between start (inclusive) and end (exclusive)

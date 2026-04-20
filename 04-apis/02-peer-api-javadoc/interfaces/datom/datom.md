# Interface Datom

**Package:** [datomic](../../peer-api-javadoc.md)

`public interface Datom`

An immutable, point-in-time fact: `[entity, attribute, value, transaction, added]`

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| [`Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`a()`](#a) | This datom's [attribute](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#defining-schema) id. |
| `boolean` | [`added()`](#added) | Is this datom added or retracted? |
| [`Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`e()`](#e) | This datom's [entity id](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entities). |
| [`Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`get(int index)`](#get) | Positional getter, as if datom is tuple of `[e a v tx added]` |
| [`Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`tx()`](#tx) | This datom's [transaction id](../../../../06-reference/02-transactions/02-transaction-data/transaction-data.md#reified-transactions). |
| [`Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`v()`](#v) | This datom's value. |

## Method Details

### e

`Object e()`

This datom's [entity id](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entities).

**Returns:** entity id

---

### a

`Object a()`

This datom's [attribute](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#defining-schema) id.

**Returns:** attribute id

---

### v

`Object v()`

This datom's value.

**Returns:** value

---

### tx

`Object tx()`

This datom's [transaction id](../../../../06-reference/02-transactions/02-transaction-data/transaction-data.md#reified-transactions).

**Returns:** transaction id

---

### added

`boolean added()`

Is this datom added or retracted?

When datoms come from [`Database.history()`](../database/database.md#history), this method can be used to distinguish additions from retractions.

**Returns:** a boolean indicating whether this datom was added or retracted

---

### get

`Object get(int index)`

Positional getter, as if datom is tuple of `[e a v tx added]`

**Parameters:** `index` — numeric index in the range [0,4]

**Returns:** the value at this position in the datom

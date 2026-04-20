# Interface Entity

**Package:** [datomic](../../peer-api-javadoc.md)

`public interface Entity`

Implements the [Entity API](../../../../06-reference/07-entities/entities.md) for associative navigation by attribute keys.

An Entity is lazy — the values of its attributes are not obtained from the db until `get` or `touch` are called, after which they are cached in the entity.

Note that entities have reference-like equality semantics — two entities are equal if they have the same id and their databases have the same id.

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| [`Database`](../database/database.md) | [`db()`](#db) | Returns the database value that is the basis for this entity. |
| [`Object`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html) | [`get(Object key)`](#get) | Gets the value of the attribute named by key; cardinality `:many` attributes will always return a collection, even when only one value. |
| [`Set<String>`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Set.html) | [`keySet()`](#keyset) | Returns the key names of the attributes. |
| [`Entity`](entity.md) | [`touch()`](#touch) | Touches all of the attributes of the entity, including any component entities recursively. |

## Method Details

### get

`Object get(Object key)`

Gets the value of the attribute named by key, and is polymorphic on key type. Cardinality `:many` attributes will always return a collection, even when only one value.

**Parameters:** `key` — a colon-prefixed string, e.g. `":user/firstName"`

**Returns:** the value(s) of that attribute, or null if none

---

### touch

`Entity touch()`

Touches all of the attributes of the entity, including any component entities recursively.

**Returns:** this Entity

---

### keySet

`Set<String> keySet()`

**Returns:** the key names of the attributes

---

### db

`Database db()`

**Returns:** the database value that is the basis for this entity

# Interface Database

`public interface Database`

An immutable, point-in-time database value.

## Nested Class Summary

| Modifier and Type | Interface | Description |
|---|---|---|
| `static interface` | [`Database.Predicate<T>`](../database-predicate/database-predicate.md) | Boolean-valued function for [filtering](#filterdatabasepredicatedatom-pred) a database. |

## Field Summary

| Modifier and Type | Field | Description |
|---|---|---|
| `static final Object` | [`AEVT`](#aevt) | Names the [AEVT index](../../../../06-reference/04-indexes/01-index-model/index-model.md#aevt). |
| `static final Object` | [`AVET`](#avet) | Names the [AVET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet). |
| `static final Object` | [`EAVT`](#eavt) | Names the [EAVT index](../../../../06-reference/04-indexes/01-index-model/index-model.md#eavt). |
| `static final Object` | [`VAET`](#vaet) | Names the [VAET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet). |

## Method Summary

| Modifier and Type | Method | Description |
|---|---|---|
| `Database` | [`asOf(Object t)`](#asof) | Returns the value of the database filtered to include data up to `t`, inclusive |
| `Long` | [`asOfT()`](#asOft) | [`asOf(Object)`](#asof) [t value](../../../../12-glossary/glossary.md#t). |
| `Attribute` | [`attribute(Object attrId)`](#attribute) | Returns information about an [attribute](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#attributes). |
| `long` | [`basisT()`](#basist) | [t value](../../../../12-glossary/glossary.md#t) of the most recent transaction in this db. |
| `Iterable<Datom>` | [`datoms(Object index, Object... components)`](#datoms) | Implements the [Datoms API](../../../../06-reference/04-indexes/01-index-model/index-model.md#datoms-api) for raw access to matching index data. |
| `Map` | [`dbStats()`](#dbstats) | Queries for database stats. |
| `Object` | [`entid(Object entityId)`](#entid) | Returns the entity id associated with any kind of entity identifier. |
| `Object` | [`entidAt(Object partition, Object timePoint)`](#entidat) | Returns a fabricated entity id in the supplied partition whose T component is at or after the supplied t |
| `Entity` | [`entity(Object entityId)`](#entity) | Returns an [entity](../../../../06-reference/07-entities/entities.md): a lazy, dynamic associative view of datoms sharing an entity id. |
| `Database` | [`filter(Database.Predicate<Datom> pred)`](#filterdatabasepredicatedatom-pred) | Returns a value of the database containing only Datoms satisfying the predicate. |
| `Database` | [`filter(Object pred)`](#filter-object-pred) |  |
| `Database` | [`history()`](#history) | Returns a history database value containing all assertions and retractions across time. |
| `String` | [`id()`](#id) | Opaque, globally unique database id. |
| `Object` | [`ident(Object idOrKey)`](#ident) | Returns the symbolic keyword associated with an id, or the key itself if passed. |
| `Stream<Object>` | [`indexPull(Object options)`](#indexpull) | Walks an index, pulling entities via `:e` if `:avet` or `:v` if `:aevt`, using the selector, returning a Stream of the results. |
| `Iterable<Datom>` | [`indexRange(Object attrid, Object start, Object end)`](#indexrange) | Returns a range of [AVET-indexed](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet) datoms. |
| `Object` | [`invoke(Object entityId, Object... args)`](#invoke) | Look up the [database function](../../../../06-reference/database-functions/database-functions.md) of the entity at `entityId`, and invoke the function with `args`. |
| `boolean` | [`isFiltered()`](#isfiltered) | Does database have a filter set with e.g. [`filter(Database.Predicate)`](#filterdatabasepredicatedatom-pred)? |
| `boolean` | [`isHistory()`](#ishistory) | True for databases created with [`history()`](#history) |
| `long` | [`nextT()`](#nextt) | next [t value](../../../../12-glossary/glossary.md#t) that will be assigned by this database. |
| `Map` | [`pull(Object pattern, Object entityId)`](#pull) | Returns a hierarchical selection of attributes for entityId. |
| `List<Map>` | [`pullMany(Object pattern, List entityIds)`](#pullmany) | Returns hierarchical selections of attributes for entityIds. |
| `Iterable<Datom>` | [`seekDatoms(Object index, Object... components)`](#seekdatoms) | Raw access to index data, starting at nearest match to input |
| `Database` | [`since(Object t)`](#since) | Returns the value of the database filtered to include only data since `t`, exclusive |
| `Long` | [`sinceT()`](#sincet) | [`since(Object)`](#since) [t value](../../../../12-glossary/glossary.md#t). |
| `Map` | [`with(List txData)`](#with) | Returns a database with `txData` applied locally in memory. |

## Field Details

### EAVT

`static final Object EAVT`

Names the [EAVT index](../../../../06-reference/04-indexes/01-index-model/index-model.md#eavt).

Pass to APIs that take an index name such as [`datoms(Object, Object...)`](#datoms).

---

### AEVT

`static final Object AEVT`

Names the [AEVT index](../../../../06-reference/04-indexes/01-index-model/index-model.md#aevt).

Pass to APIs that take an index name such as [`datoms(Object, Object...)`](#datoms).

---

### AVET

`static final Object AVET`

Names the [AVET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet).

Pass to APIs that take an index name such as [`datoms(Object, Object...)`](#datoms).

---

### VAET

`static final Object VAET`

Names the [VAET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet).

Pass to APIs that take an index name such as [`datoms(Object, Object...)`](#datoms).

---

## Method Details

### id

`String id()`

Opaque, globally unique database id.

**Returns:** the database id

---

### basisT

`long basisT()`

[t value](../../../../12-glossary/glossary.md#t) of the most recent transaction in this db.

**Returns:** a t value

---

### nextT

`long nextT()`

next [t value](../../../../12-glossary/glossary.md#t) that will be assigned by this database.

**Returns:** a t value

---

### asOfT

`Long asOfT()`

[`asOf(Object)`](#asof) [t value](../../../../12-glossary/glossary.md#t).

**Returns:** a t value, or null

---

### sinceT

`Long sinceT()`

[`since(Object)`](#since) [t value](../../../../12-glossary/glossary.md#t).

**Returns:** a t value, or null

---

### isHistory

`boolean isHistory()`

True for databases created with [`history()`](#history)

**Returns:** true for history databases

---

### with

`Map with(List txData)`

Returns a database with `txData` applied locally in memory. It is as if the data was applied in a [transaction](../../../../06-reference/02-transactions/transactions.md), but no actual transaction takes place.

**Parameters:**
- `txData` — in the same format as expected by [`transact`](../connection/connection.md#transact)

**Returns:** a map as returned by transact

---

### asOf

`Database asOf(Object t)`

Returns the value of the database filtered to include data up to `t`, inclusive

**Parameters:**
- `t` — a [time-point](../../../../12-glossary/glossary.md#time-point)

**Returns:** the value of the database as of some point t, inclusive

---

### since

`Database since(Object t)`

Returns the value of the database filtered to include only data since `t`, exclusive

**Parameters:**
- `t` — a [time-point](../../../../12-glossary/glossary.md#time-point)

**Returns:** the value of the database since some point t, exclusive

---

### history

`Database history()`

Returns a history database value containing all assertions and retractions across time.

A history database can be used for [`datoms(Object, Object...)`](#datoms) and [`indexRange(Object, Object, Object)`](#indexrange), for [queries](../../classes/peer/peer.md#query), and for [`asOf(Object)`](#asof) and [`since(Object)`](#since).

A history database *cannot* be used with APIs that require a single point-in-time, i.e. [`entity(Object)`](#entity) or [`with(List)`](#with).

Note that queries will return all of the additions and retractions, which can be distinguished by [`Datom.added()`](../datom/datom.md#added).

**Returns:** a history Database

---

### filter(Database.Predicate\<Datom\> pred)

`Database filter(Database.Predicate<Datom> pred)`

Returns a value of the database containing only Datoms satisfying the predicate. The predicate will be passed the unfiltered db and a Datom. Chained calls to `filter` compose predicates with logical 'and'.

**Parameters:**
- `pred` — a `Predicate<Datom>` or `clojure fn`

**Returns:** the value of the database satisfying the predicate

**Since:** 0.8.3627

---

### filter(Object pred)

`Database filter(Object pred)`

---

### isFiltered

`boolean isFiltered()`

Does database have a filter set with e.g. [`filter(Database.Predicate)`](#filterdatabasepredicatedatom-pred)?

**Returns:** true if db has a filter

**Since:** 0.8.3627

---

### entity

`Entity entity(Object entityId)`

Returns an [entity](../../../../06-reference/07-entities/entities.md): a lazy, dynamic associative view of datoms sharing an entity id.

**Parameters:**
- `entityId` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers)

**Returns:** an [`Entity`](../entity/entity.md)

---

### attribute

`Attribute attribute(Object attrId)`

Returns information about an [attribute](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#attributes).

**Parameters:**
- `attrId` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers) for an attribute

**Returns:** an [`Attribute`](../attribute/attribute.md)

**Since:** 0.9.4470

---

### ident

`Object ident(Object idOrKey)`

Returns the symbolic keyword associated with an id, or the key itself if passed.

**Parameters:**
- `idOrKey` — an id or keyword

**Returns:** a keyword, or nil if not found

---

### entid

`Object entid(Object entityId)`

Returns the entity id associated with any kind of entity identifier.

**Parameters:**
- `entityId` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers)

**Returns:** an id, or nil if not found

---

### entidAt

`Object entidAt(Object partition, Object timePoint)`

Returns a fabricated entity id in the supplied partition whose T component is at or after the supplied t. Entity ids sort by partition, then T component, such T components interleaving with transaction numbers. Thus this method can be used to fabricate a time-based entity id component for use in `seekDatoms`.

**Parameters:**
- `partition` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers) for a partition
- `timePoint` — a [time-point](../../../../12-glossary/glossary.md#time-point)

**Returns:** a fabricated entity id at or after some point t

---

### invoke

`Object invoke(Object entityId, Object... args)`

Look up the [database function](../../../../06-reference/database-functions/database-functions.md) of the entity at `entityId`, and invoke the function with `args`.

**Parameters:**
- `entityId` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers)
- `args` — the arguments to the database function

**Returns:** the return value of the database function

---

### datoms

`Iterable<Datom> datoms(Object index, Object... components)`

Implements the [Datoms API](../../../../06-reference/04-indexes/01-index-model/index-model.md#datoms-api) for raw access to matching index data. The index must be supplied, and, optionally, one or more leading components of the index can be supplied to narrow the result.

[EAVT](../../../../06-reference/04-indexes/01-index-model/index-model.md#eavt) and [AEVT](../../../../06-reference/04-indexes/01-index-model/index-model.md#aevt) indexes will contain all datoms. [AVET](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet) will contain datoms for attributes where either `:db/index` or `:db/unique` are true. [VAET](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet) will contain datoms for attributes of `:db.type/ref` — it is the reverse index.

**Parameters:**
- `index` — one of [`EAVT`](#eavt), [`AEVT`](#aevt), [`AVET`](#avet), or [`VAET`](#vaet)
- `components` — supply any datom components to match, in order corresponding to the index

**Returns:** the datoms in the specified index matching the specified components

---

### seekDatoms

`Iterable<Datom> seekDatoms(Object index, Object... components)`

Raw access to index data, starting at nearest match to input.

Arguments are the same as to [`datoms(Object, Object...)`](#datoms), but their interpretation is different in two important ways:

1. The match need not be exact. Results will begin with the closest matching datom.
2. No termination. Results will continue all the way to the end of the index.

`seekDatoms` is for more advanced applications, and [`datoms(Object, Object...)`](#datoms) should be preferred wherever it is adequate.

`seekDatoms` is typically used in conjunction with [`entidAt(Object, Object)`](#entidat) to implement [new entity scans](../../../../06-reference/04-indexes/01-index-model/index-model.md#new-entity-scans).

**Parameters:**
- `index` — one of [`EAVT`](#eavt), [`AEVT`](#aevt), [`AVET`](#avet), or [`VAET`](#vaet)
- `components` — supply any datom components to search for, in order corresponding to the index

**Returns:** all of the datoms in the specified index at or after the specified components

---

### indexRange

`Iterable<Datom> indexRange(Object attrid, Object start, Object end)`

Returns a range of [AVET-indexed](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet) datoms.

**Parameters:**
- `attrid` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers) naming an indexed attribute
- `start` — start value or null if from beginning
- `end` — end value (non-inclusive), or null if through end

**Returns:** an `Iterable` over `Datom` positioned between start (inclusive) and end (exclusive)

---

### pull

`Map pull(Object pattern, Object entityId)`

Returns a hierarchical selection of attributes for entityId.

**Parameters:**
- `pattern` — a [pattern](../../../../06-reference/03-query-and-pull/03-pull/pull.md), or a String containing a pattern serialized into edn
- `entityId` — an [entity identifier](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers)

**Returns:** a map containing a selection for the entityId passed in

**Since:** 0.9.5040

---

### indexPull

`Stream<Object> indexPull(Object options)`

Walks an index, pulling entities via `:e` if `:avet` or `:v` if `:aevt`, using the selector, returning a Stream of the results.

**Parameters:**
- `options` — a map describing the indexPull, serialized into edn, with the following keys:

| Key | Description |
|---|---|
| `:index` | `:avet` or `:aevt` |
| `:selector` | a pull selector (see `pull`) |
| `:start` | A vector in the same order as the index indicating the initial position. At least `:a` must be specified. Iteration is limited to datoms matching `:a`. |
| `:reverse` | optional; when true, iterate the index in reverse order |

**Returns:** a `Stream` of the indexPull results

**Since:** 0.9.6079

---

### pullMany

`List<Map> pullMany(Object pattern, List entityIds)`

Returns hierarchical selections of attributes for entityIds.

**Parameters:**
- `pattern` — a [pattern](../../../../06-reference/03-query-and-pull/03-pull/pull.md), or a String containing a pattern serialized into edn
- `entityIds` — a list of [entity identifiers](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entity-identifiers)

**Returns:** a list of maps containing a selection for each entityId passed in

**Since:** 0.9.5040

---

### dbStats

`Map dbStats()`

Queries for database stats.

**Returns:** a map with at least the following keys:

| Key | Description |
|---|---|
| `:datoms` | total count of datoms in the (history) database |

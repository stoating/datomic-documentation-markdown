# Interface Attribute

**Package:** [datomic](../../peer-api-javadoc.md)

`public interface Attribute`

Programmatic representation of a [schema attribute](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#attributes).

Attribute information always resides in memory, so using this interface is more efficient than accessing the same information from the database via e.g. query.

**Since:** 0.9.4470

## Field Summary

| Modifier and Type | Field |
|-------------------|-------|
| `static final Object` | `CARDINALITY_MANY` |
| `static final Object` | `CARDINALITY_ONE` |
| `static final Object` | `TYPE_BIGDEC` |
| `static final Object` | `TYPE_BIGINT` |
| `static final Object` | `TYPE_BOOLEAN` |
| `static final Object` | `TYPE_BYTES` |
| `static final Object` | `TYPE_DOUBLE` |
| `static final Object` | `TYPE_FLOAT` |
| `static final Object` | `TYPE_FN` |
| `static final Object` | `TYPE_INSTANT` |
| `static final Object` | `TYPE_KEYWORD` |
| `static final Object` | `TYPE_LONG` |
| `static final Object` | `TYPE_REF` |
| `static final Object` | `TYPE_STRING` |
| `static final Object` | `TYPE_URI` |
| `static final Object` | `TYPE_UUID` |
| `static final Object` | `UNIQUE_IDENTITY` |
| `static final Object` | `UNIQUE_VALUE` |

## Method Summary

| Modifier and Type | Method | Description |
|-------------------|--------|-------------|
| `Object` | `cardinality()` | The attribute's [cardinality](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#cardinality) |
| `boolean` | `hasAVET()` | Does this attribute *currently* have an [AVET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet)? |
| `boolean` | `hasFulltext()` | Does this attribute have a fulltext index? |
| `boolean` | `hasNoHistory()` | Is this a [noHistory](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#nohistory) attribute? |
| `Object` | `id()` | The attribute's [entity id](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entities) |
| `Object` | `ident()` | The attribute's [ident](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#idents) (programmatic name) |
| `boolean` | `isComponent()` | Is this a [component](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#component) attribute? |
| `boolean` | `isIndexed()` | Is this attribute configured for an [AVET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet)? |
| `Object` | `unique()` | Type of the attribute's [unique index](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#unique-identities), if any. |
| `Object` | `valueType()` | The attribute's [value type](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#value-types) |

## Field Details

### CARDINALITY_MANY

`static final Object CARDINALITY_MANY`

### CARDINALITY_ONE

`static final Object CARDINALITY_ONE`

### UNIQUE_IDENTITY

`static final Object UNIQUE_IDENTITY`

### UNIQUE_VALUE

`static final Object UNIQUE_VALUE`

### TYPE_BIGDEC

`static final Object TYPE_BIGDEC`

### TYPE_BIGINT

`static final Object TYPE_BIGINT`

### TYPE_BOOLEAN

`static final Object TYPE_BOOLEAN`

### TYPE_BYTES

`static final Object TYPE_BYTES`

### TYPE_DOUBLE

`static final Object TYPE_DOUBLE`

### TYPE_FN

`static final Object TYPE_FN`

### TYPE_FLOAT

`static final Object TYPE_FLOAT`

### TYPE_INSTANT

`static final Object TYPE_INSTANT`

### TYPE_KEYWORD

`static final Object TYPE_KEYWORD`

### TYPE_LONG

`static final Object TYPE_LONG`

### TYPE_REF

`static final Object TYPE_REF`

### TYPE_STRING

`static final Object TYPE_STRING`

### TYPE_URI

`static final Object TYPE_URI`

### TYPE_UUID

`static final Object TYPE_UUID`

## Method Details

### id

`Object id()`

The attribute's [entity id](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#entities)

**Returns:** an entity id

### ident

`Object ident()`

The attribute's [ident](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#idents) (programmatic name)

**Returns:** an ident

### valueType

`Object valueType()`

The attribute's [value type](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#value-types)

**Returns:** a value type

### cardinality

`Object cardinality()`

The attribute's [cardinality](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#cardinality)

**Returns:** either `CARDINALITY_MANY` or `CARDINALITY_ONE`

### unique

`Object unique()`

Type of the attribute's [unique index](../../../../06-reference/01-schema/04-identity-and-uniqueness/identity-and-uniqueness.md#unique-identities), if any.

**Returns:** one of `UNIQUE_IDENTITY`, `UNIQUE_VALUE`, or null

### isComponent

`boolean isComponent()`

Is this a [component](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#component) attribute?

**Returns:** true if `:db/isComponent` true for this attribute

### isIndexed

`boolean isIndexed()`

Is this attribute configured for an [AVET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet)?

**Returns:** true if `:db/index` true for this attribute, or attribute is unique

### hasAVET

`boolean hasAVET()`

Does this attribute *currently* have an [AVET index](../../../../06-reference/04-indexes/01-index-model/index-model.md#avet)?

When you [alter](../../../../06-reference/01-schema/02-changing-schema/changing-schema.md#schema-alteration) an existing schema, indexes are created in the background after setting `:db/index` to true. This method returns true once a recently-added index is ready for use.

**Returns:** true if AVET index available for this attribute

### hasNoHistory

`boolean hasNoHistory()`

Is this a [noHistory](../../../../06-reference/01-schema/01-schema-reference/schema-reference.md#nohistory) attribute?

**Returns:** true if `:db/noHistory` true for this attribute

### hasFulltext

`boolean hasFulltext()`

Does this attribute have a fulltext index?

**Returns:** true if `:db/fulltext` true for this attribute

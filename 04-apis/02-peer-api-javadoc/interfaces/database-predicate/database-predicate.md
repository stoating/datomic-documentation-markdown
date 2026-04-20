# Interface Database.Predicate&lt;T&gt;

**Package:** [datomic](../../peer-api-javadoc.md)

**Type Parameters:** `T` - Datom type

**Enclosing interface:** [Database](../database/database.md)

`public static interface Database.Predicate<T>`

Boolean-valued function for [filtering](../database/database.md#filter) a database.

**Since:** 0.8.3627

## Method Summary

| Modifier and Type | Method | Description |
|-------------------|--------|-------------|
| `boolean` | `apply(Database db, T val)` | Database-filtering predicate. |

## Method Details

### apply

`boolean apply(Database db, T val)`

Database-filtering predicate.

**Parameters:**
- `db` - a [Database](../database/database.md)
- `val` - a [Datom](../datom/datom.md)

**Returns:** true if datom matches the predicate.

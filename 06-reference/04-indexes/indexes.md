# Indexes

Any system that accumulates data and offers the ability to ask questions about that data will need an efficient way to retrieve specific information. In databases, that takes the form of indexes. Indexes afford the database access to the underlying data in a format that is optimized for read-time access. A covering index is one that contains actual data values, not just pointers to where those values can be found.

Datomic maintains four covering indexes that contain ordered sets of datoms. Each of these indexes is named based on the sort order used. E, A, and V are always sorted in ascending order, while T is always in descending order:

| Index | Sort order | Contains |
|---|---|---|
| `EAVT` | entity / attribute / value / tx | all datoms |
| `AEVT` | attribute / entity / value / tx | all datoms |
| `AVET` | attribute / value / entity / tx | Pro: `:db/unique` or `:db/index` attributes; Cloud/Local: all datoms |
| `VAET` | value / attribute / entity / tx | `:db.type/ref` attributes |

Datomic indexes are used behind the scenes in [Query](../03-query-and-pull/query-and-pull.md), [Entity API](../07-entities/entities.md), and [Pull API](../03-query-and-pull/03-pull/pull.md). For access patterns that aren't well suited for query, Datomic also provides [Index APIs](../../04-apis/07-index-apis/index-apis.md) that offer the ability to retrieve data directly from indexes.

This section documents what Datomic's indexes are, how they are maintained, and how to access them.

- [Index Model](index-model.md)
- [Background Indexing](background-indexing.md)

# Read

## Database Values

Datomic maintains the entire history of your data. From this, you can
query against a database value as of a particular point in time.

The *db* API returns the latest database value from a connection.

```clojure
(def db (d/db conn))
```

An analogy with source control is helpful here. A Datomic connection
references the entire history of your data, analogous to a source code
repository. A database value from *db* is analogous to a checkout.

## Pull

If you know an entity id, you can use the [pull API](../../../06-reference/03-query-and-pull/03-pull/pull.md) to return
information about that entity and related entities. Better still, if
the entity has a unique attribute, you do not even need to know its
entity id. A lookup ref is a two-element list of unique attribute +
value that uniquely identifies an entity, e.g.

```clojure
[:inv/sku "SKU-42"]
```

The following call pulls the color, type, and size for *SKU-42*:

```clojure
(d/pull
 (d/db conn)
 [{:inv/color [:db/ident]}
  {:inv/size [:db/ident]}
  {:inv/type [:db/ident]}]
 [:inv/sku "SKU-42"])
```

```
#:inv{:color #:db{:ident :blue}, 
      :size #:db{:ident :large}, 
      :type #:db{:ident :dress}}
```

Note that the arguments and return value of pull are both just
ordinary data structures, i.e. lists and maps.

### Database as a Value

[Previously when data was transacted](../02-assertion/assertion.md) the result was stored in `sample-data-transaction`.

`(:db-before sample-data-transaction)` gets the database value before the transaction.

Attempting to pull against the database *before* the transaction shows that the data does not exist in the `db`.

```clojure
(d/pull
 (:db-before sample-data-transaction)
 [{:inv/color [:db/ident]}
  {:inv/size [:db/ident]}
  {:inv/type [:db/ident]}]
 [:inv/sku "SKU-42"])
```

```
{}
```

Pulling against the db value in `(:db-after sample-data-transaction)` will pull against a `db` with the data
transacted [previously in the tutorial](../02-assertion/assertion.md).

## Query

Storing and retrieving data by unique id is useful, but a database
needs also to provide declarative, logic-based query. Datomic uses
datalog with negation, which has expressive power similar to SQL +
recursion.

The following query finds the skus of all products that share a color
with *SKU-42*:

```clojure
(d/q
 '[:find ?sku
   :where
   [?e :inv/sku "SKU-42"]
   [?e :inv/color ?color]
   [?e2 :inv/color ?color]
   [?e2 :inv/sku ?sku]]
 db)
```

```
[["SKU-42"] 
 ["SKU-32"] 
 ...]
```

Note that the arguments and return value of `q` are both just ordinary
data structures, i.e. lists and maps.

In the `:where` clauses, each list further constrains the results. For
each list:

- The first element matches the entity id
- The second element matches an attribute
- The third element matches an attribute's value

Symbols beginning with a question mark are Datalog *variables*. When
the same symbol occurs more than once, it causes a join. In the query
above

- `?e` joins *SKU-42* to its color
- `?e2` joins to all entities sharing the color
- `?sku` retrieves the SKU for every `?e2`

Now we are confident that we can get basic inventory in and out. Just
in time, too, because our stakeholders are back with [more feature requests](../04-accumulate/accumulate.md).

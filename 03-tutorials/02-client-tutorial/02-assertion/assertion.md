# Assertion

## Create and Connect

An assertion requires a database to exist and a connection.

- Utilize a configuration map as described [in the client API tutorial](../01-client-api/client-api.md#create-a-client):

```clojure
(require '[datomic.client.api :as d])

(def cfg {:server-type :dev-local
          :system "datomic-samples"})

(def client (d/client cfg))
```

- Create a new database with [`create-database`](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#create-database):

```clojure
(d/create-database client {:db-name "tutorial"})
```

- Acquire a connection to the database with [`connect`](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#connect):

```clojure
(def conn (d/connect client {:db-name "tutorial"}))
```

## List and Map Forms

An assertion adds a single atomic fact to Datomic. Assertions are represented by ordinary data structures (lists or maps). Our inventory database will need to have enumerated types for various product attributes such as color.

- Let's start with an [edn](https://github.com/edn-format/edn) list that asserts the color green:

```clojure
[:db/add "foo" :db/ident :green]
```

- `:db/add` specifies that this is an assertion
- "Foo" is a temporary entity id for the new entity
- `:db/ident` is an *attribute* used for programmatic identifiers
- `:green` is the datom's *value*

- The same datom can be represented by a map:

```clojure
{:db/ident :green}
```

Maps imply (and are equivalent to) a set of assertions, all about the same entity.

## Transactions

Datomic databases are updated via [ACID](../../../06-reference/02-transactions/05-acid/acid.md) transactions, which add a set of datoms to the database. Execute the code below at a [Clojure REPL](https://clojure.org/guides/repl/introduction) to add colors to the inventory database in a single transaction.

The transaction below adds four colors to the database:

```clojure
(d/transact
 conn 
 {:tx-data [{:db/ident :red}
            {:db/ident :green}
            {:db/ident :blue}
            {:db/ident :yellow}]})
```

```
;; returns a big map
```

A successful transaction returns a map with information about the transaction and the state of the database. It will be explored later.

## Programming with Data

In addition to colors, our inventory database will also track sizes and types of items. Since we are programming with data, it is easy to write a helper function to make these transactions more concise.

- The `make-idents` function shown below will take a collection of keywords, and return transaction-ready maps:

```clojure
(defn make-idents
  [x]
  (mapv #(hash-map :db/ident %) x))
```

- You can quickly notice that this works by trying it out at the REPL:

```clojure
(def sizes [:small :medium :large :xlarge])
(make-idents sizes)
```

```
[#:db{:ident :small} #:db{:ident :medium} 
 #:db{:ident :large} #:db{:ident :xlarge}]
```

Note that because `make-idents` function takes and returns pure data, no database is necessary to develop and test this function.

The `#:db` before the map indicates a [namespaced map](https://clojure.org/guides/weird_characters#_and_namespace_map_syntax).

- Let's put types, colors, and sizes into the database and define a collection of colors we already added:

```clojure
(def types [:shirt :pants :dress :hat])
(def colors [:red :green :blue :yellow])

(d/transact conn {:tx-data (make-idents sizes)})
(d/transact conn {:tx-data (make-idents colors)})
(d/transact conn {:tx-data (make-idents types)})
```

## Schema

So far we have used only built-in schema such as `:db/ident`. Now we want to add some inventory-specific attributes:

- *sku*, a unique string identifier for a particular product
- *color*, a reference to a color entity
- *size*, a reference to a size entity
- *type*, a reference to a type entity

In Datomic, schema are entities just like program data. A schema entity must include:

- [:db/ident](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbident), a programmatic name
- [:db/valueType](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbvaluetype), a reference to an entity that specifies what type the attribute allows
- [:db/cardinality](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbcardinality), a reference to an entity that specifies whether a particular entity can possess more than one value for the attribute at a given time.

So we can add our schema like this:

```clojure
(def schema-1
  [{:db/ident :inv/sku
    :db/valueType :db.type/string
    :db/unique :db.unique/identity
    :db/cardinality :db.cardinality/one}
   {:db/ident :inv/color
    :db/valueType :db.type/ref
    :db/cardinality :db.cardinality/one}
   {:db/ident :inv/size
    :db/valueType :db.type/ref
    :db/cardinality :db.cardinality/one}
   {:db/ident :inv/type
    :db/valueType :db.type/ref
    :db/cardinality :db.cardinality/one}])
(d/transact conn {:tx-data schema-1})
```

Notice that the `:inv/sku` attribute also has a `:db/unique` value. This specifies that every `:inv/sku` must be unique within the database.

## Sample Data

Now let's make some sample data. Again, no special API is necessary, we can just use ordinary Clojure collections. The following expression creates one example inventory entry for each combination of color, size, and type:

```clojure
;;; Defined earlier, but repeated for clarity
(def colors [:red :green :blue :yellow])
(def sizes [:small :medium :large :xlarge])
(def types [:shirt :pants :dress :hat])

(defn create-sample-data
  "Create a vector of maps of all permutations of args"
  [colors sizes types]
  (->> (for [color colors size sizes type types]
         {:inv/color color
          :inv/size size
          :inv/type type})
       (map-indexed
        (fn [idx map]
          (assoc map :inv/sku (str "SKU-" idx))))
       vec)) ;; 64 (4 x 4 x 4) maps

@(def sample-data (create-sample-data colors sizes types))

@(def sample-data-transaction (d/transact conn {:tx-data sample-data}))
```

Now that we have asserted some data, let's look at some different ways we can [retrieve it](../03-read/read.md).

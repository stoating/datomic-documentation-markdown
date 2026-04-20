# Transact Schema

Previously: you learned how to [connect to a database](../02-connect-to-a-database/connect-to-a-database.md).
This tutorial expects that you have a running REPL where you have already connected to the database "hello".

One of the great things about Datomic is that everything is data,
including the definition of the schema for your database.
Because it is just data, it can be created and manipulated just like
any other data you put into the database.
To add a new schema, you issue a transaction that carries the new schema definition.

This section will create a basic model for movies: a movie can
have a title, a release year, and a genre.
Title and genre are represented as *strings*, while release year is an *integer*.
In a relational database, each of those (title, release year, and genre) would be considered "fields" in a "table".

In Datomic, each of those is an attribute, which can be associated with an entity.
To start adding movie data to our database, we'll have to tell it about those three attributes.
And, since schema is just data, you describe your custom attributes using Datomic's [built-in attributes](../../../06-reference/01-schema/01-schema-reference/schema-reference.md).

Every attribute definition has [three required attributes](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#defining-schema).

You can also supply the optional `:db/doc` attribute, which stores a docstring of the attribute which you can query for later.

- Our first attribute, "title", will be defined as:

```clojure
{:db/ident :movie/title
 :db/valueType :db.type/string
 :db/cardinality :db.cardinality/one
 :db/doc "The title of the movie"}
```

- Similarly, "genre" will be:

```clojure
{:db/ident :movie/genre
 :db/valueType :db.type/string
 :db/cardinality :db.cardinality/one
 :db/doc "The genre of the movie"}
```

- Since "year" will be stored as a number, we'll need to use the `:db.type/long` for its `:db/valueType`:

```clojure
{:db/ident :movie/release-year
 :db/valueType :db.type/long
 :db/cardinality :db.cardinality/one
 :db/doc "The year the movie was released in theaters"}
```

You could choose to send each of these attributes to the database in
individual transactions. We'll batch them up into a single transaction to be more efficient and combine the three maps into a single vector of maps. Let's bind our
schema vector to a var called `movie-schema`:

```clojure
(def movie-schema [{:db/ident :movie/title
                    :db/valueType :db.type/string
                    :db/cardinality :db.cardinality/one
                    :db/doc "The title of the movie"}

                   {:db/ident :movie/genre
                    :db/valueType :db.type/string
                    :db/cardinality :db.cardinality/one
                    :db/doc "The genre of the movie"}

                   {:db/ident :movie/release-year
                    :db/valueType :db.type/long
                    :db/cardinality :db.cardinality/one
                    :db/doc "The year the movie was released in theaters"}])
```

```
#'user/movie-schema
```

Now that we have our attribute definitions ready to go, we
have to issue a transaction via the Peer library, using the *transact* method.
Transact takes two parameters:

- An active connection
- A collection of transaction data, formatted in [list or map form](../../../06-reference/02-transactions/02-transaction-data/transaction-data.md#transaction-data)

- Go ahead and transact the schema:

```clojure
@(d/transact conn movie-schema)
```

```
  {:db-before datomic.db.Db@624b73ed, :db-after datomic.db.Db@f0d17d2a,
   :tx-data
    [#datom[13194139534312 50 #inst "2017-02-16T16:24:50.487-00:00" 13194139534312 true] 
     #datom[63 10 :movie/title 13194139534312 true] 
     #datom[63 40 23 13194139534312 true] 
     #datom[63 41 35 13194139534312 true] 
     #datom[63 62 "The title of the movie" 13194139534312 true]
     #datom[64 10 :movie/genre 13194139534312 true]
     #datom[64 40 23 13194139534312 true] 
     #datom[64 41 35 13194139534312 true] 
     #datom[64 62 "The genre of the movie" 13194139534312 true] 
     #datom[65 10 :movie/release-year 13194139534312 true] 
     #datom[65 40 22 13194139534312 true] 
     #datom[65 41 35 13194139534312 true] 
     #datom[65 62 "The year the movie was released in theaters" 13194139534312 true]
     #datom[0 13 65 13194139534312 true]
     #datom[0 13 64 13194139534312 true]
     #datom[0 13 63 13194139534312 true]],
   :tempids {-9223301668109598144 63, -9223301668109598143 64, -9223301668109598142 65}}
```

The presence of the `:db-before` and `:db-after` values shows that new information was transmitted and persisted in your database.
You can learn more about the details of this return value by reading the details of [transact](../../../04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md#transact) in the reference guide.

In the next section, we'll start [adding movies into the database](../04-transact-data/transact-data.md).

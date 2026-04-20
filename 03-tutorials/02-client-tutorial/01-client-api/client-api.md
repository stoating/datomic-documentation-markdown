# Client API Tutorial

This tutorial introduces the Datomic Client API. You will:

- [Create a database](#create-a-database)
- [Transact schema](#transact-schema)
- [Transact data](#transact-data)
- [Query the database](#query)
- Optionally [delete the database](#delete-a-database-optional) when you are done

## Prerequisites

This tutorial assumes that you have [setup Datomic Local](../../../01-setup/03-local-setup/local-setup.md) and started a REPL with Datomic Local on your classpath, or you have [launched](../../../01-setup/02-cloud-setup/02-cloud-setup/cloud-setup.md) a Datomic Cloud system and know how to start a Clojure REPL with the [Datomic client API installed](../../../02-accessing/02-client-library/client-library.md#installing-the-client-library).

## Create a Client

### Using Datomic Local

To interact with Datomic, you must first create a [datomic.client.api/client](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#client).

In your REPL, execute:

```clojure
(require '[datomic.client.api :as d])

(def cfg {:server-type :datomic-local
          :system "datomic-samples"})

(def client (d/client cfg))
```

### Using Datomic Cloud

To [connect](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#connect) to Datomic Cloud, you will need the following information:

- `:region` is the AWS region in which you've started Datomic Cloud.
- `:system` is your Datomic system's name.
- `:endpoint` is your system's client endpoint. Check your [CloudFormation outputs](../../../05-operation/02-cloud/11-how-to/how-to.md#find-template-outputs) for `ClientApiGatewayEndpoint`.

Use this information to create a client with [datomic.client.api/client](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#client).

Even though the endpoint is public, client access is securely managed by [IAM permissions](../../../05-operation/02-cloud/06-access-control/access-control.md#how-datomic-access-control-works).

```clojure
(require '[datomic.client.api :as d])

(def cfg {:server-type :cloud
          :region "<your AWS Region>" ;; e.g. us-east-1
          :system "<system name>"
          :creds-profile "<your_aws_profile_if_not_using_the_default>"
          :endpoint "<your endpoint>"})

(def client (d/client cfg))
```

Warnings may occur. Do not be alarmed as they will not affect functionality during this tutorial.

## Create a Database

- Create a new database with [datomic.client.api/create-database](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#create-database):

```clojure
(d/create-database client {:db-name "movies"})
```

- Now you're ready to connect to your newly created database using [datomic.client.api/connect](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#connect):

```clojure
(def conn (d/connect client {:db-name "movies"}))
```

The next step will be to define some schema for your new database.

Schema defines the set of possible attributes that can be associated with an entity.
We'll need to provide 3 attributes: [db/ident](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbident), [db/valueType](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbvaluetype) and [db/cardinality](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbcardinality).
[db/doc](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbdoc) will also be provided for documentation.

## Transact Schema

Now we need to create a [schema](../../../06-reference/01-schema/01-schema-reference/schema-reference.md).

- Define the following small schema for a database about movies:

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

- Now transact the schema using [datomic.client.api/transact](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#transact).

```clojure
(d/transact conn {:tx-data movie-schema})
```

```
{:db-before ..., 
 :db-after ..., 
 :tx-data [...], ;; data added to the database
 :tempids {}}
```

You should get back [a response](../../../06-reference/02-transactions/03-processing-transactions/processing-transactions.md) as shown above.

## Transact Data

- Now you can define some movies to add to the database utilizing the [schema](../../../06-reference/01-schema/01-schema-reference/schema-reference.md#defining-schema) we defined earlier:

```clojure
(def first-movies [{:movie/title "The Goonies"
                    :movie/genre "action/adventure"
                    :movie/release-year 1985}
                   {:movie/title "Commando"
                    :movie/genre "thriller/action"
                    :movie/release-year 1985}
                   {:movie/title "Repo Man"
                    :movie/genre "punk dystopia"
                    :movie/release-year 1984}])
```

- [Transact](../../../06-reference/02-transactions/03-processing-transactions/processing-transactions.md) the movies into the database:

```clojure
(d/transact conn {:tx-data first-movies})
```

```
{:db-before ... 
 :db-after ...
 :tx-data [...], 
 :tempids {}}
```

You should see a response similar to the above with different data.

## Query

- Get a current value for the database with [datomic.client.api/db](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#db):

```clojure
(def db (d/db conn))
```

- Now create a [query](../../../06-reference/03-query-and-pull/01-executing-queries/executing-queries.md) for all movie titles:

```clojure
(def all-titles-q '[:find ?movie-title 
                    :where [_ :movie/title ?movie-title]])
```

- And execute the query with the value of the database using [datomic.client.api/q](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#q):

```clojure
(d/q all-titles-q db)
```

```
[["Commando"] ["The Goonies"] ["Repo Man"]]
```

If your database has a large number of movies, it may be prudent to use [qseq](../../../06-reference/03-query-and-pull/01-executing-queries/executing-queries.md#qseq) to return a lazy sequence quickly
rather than waiting for the full results to build and return.

## Delete a Database (Optional)

When you are done with this tutorial, you can use [datomic.client.api/delete-database](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#delete-database) to delete the *movies* database:

```clojure
(d/delete-database client {:db-name "movies"})
```

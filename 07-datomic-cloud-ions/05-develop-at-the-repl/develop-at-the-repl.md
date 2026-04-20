# Develop

## Develop at the REPL

You can develop your ion application interactively at the REPL.

The application code for this tutorial is already written so we will use the REPL to explore the code.

### Configure Connection

The ion-starter project keeps connection arguments in a resource file `resources/datomic/ion/starter/config.edn`. Edit this file to include the [connection arguments](../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#client) for your system.

### Test Your Connection

The core of the tutorial application lives in the [starter.clj](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter.clj) namespace. Start a REPL from the root of the project to explore and test these functions:

- First, load some namespace aliases:

```clojure
(load-file "siderail/user.repl")
```

- Verify that you can get a client object:

```clojure
(def client (starter/get-client))
```

If this call succeeds, continue to the next step. Otherwise, return to [configure connection](#configure-connection).

### Setup DB and Load Dataset

The `ensure-sample-dataset` function idempotently creates a database and loads the schema and some sample data. Invoke this function once before using any of the tutorial data functions.

[Review the `ensure-sample-dataset` function](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter.clj#L39), and then run it at the REPL:

```clojure
(starter/ensure-sample-dataset)
```

```clojure
:loaded
```

### Test Data Functions at the REPL

The inventory application exposes two simple data functions: `get-schema` returns the database schema, and `get-items-by-type` returns inventory items by type (`:dress`, `:hat`, `:pants`, or `:shirt`).

[Review the data functions](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter.clj), and then test that these functions work at the REPL:

```clojure
(def conn (starter/get-connection))

(try
  (def db (d/db conn))
  (catch Exception e
    (println "Failed to get db:" e)))
```

```clojure
{:t 16, :next-t 17, :db-name "datomic-docs-tutorial", :database-id "ab481fe5-b6eb-4c97-8997-7d6a11809b6d", :type :datomic.client/db}
```

```clojure
(starter/get-schema db)
```

```clojure
#db{:id 39, :ident :fressian/tag, :valueType :db.type/keyword, :cardinality :db.cardinality/one, :doc "Keyword-valued attribute of a value type that specifies the underlying fressian type used for serialization."}
#db{:id 73, :ident :inv/sku, :valueType :db.type/string, :cardinality :db.cardinality/one, :unique #db{:id 38, :ident :db.unique/identity}}
#db{:id 74, :ident :inv/color, :valueType :db.type/keyword, :cardinality :db.cardinality/one}
#db{:id 75, :ident :inv/size, :valueType :db.type/keyword, :cardinality :db.cardinality/one}
#db{:id 76, :ident :inv/type, :valueType :db.type/keyword, :cardinality :db.cardinality/one}
#db{:id 77, :ident :order/items, :valueType :db.type/ref, :cardinality :db.cardinality/many, :isComponent true}
#db{:id 78, :ident :item/id, :valueType :db.type/ref, :cardinality :db.cardinality/one}
#db{:id 79, :ident :item/count, :valueType :db.type/long, :cardinality :db.cardinality/one}
#db{:id 80, :ident :inv/count, :valueType :db.type/long, :cardinality :db.cardinality/one}
```

```clojure
(starter/get-items-by-type db :shirt '[:inv/sku :inv/color :inv/size])
```

```clojure
[[#:inv{:sku "SKU-0", :color :red, :size :small}]
 [#:inv{:sku "SKU-4", :color :red, :size :medium}]
 [#:inv{:sku "SKU-8", :color :red, :size :large}]
 [#:inv{:sku "SKU-12", :color :red, :size :xlarge}]
 [#:inv{:sku "SKU-16", :color :green, :size :small}]
 [#:inv{:sku "SKU-20", :color :green, :size :medium}]
 [#:inv{:sku "SKU-24", :color :green, :size :large}]
 [#:inv{:sku "SKU-28", :color :green, :size :xlarge}]
 [#:inv{:sku "SKU-32", :color :blue, :size :small}]
 [#:inv{:sku "SKU-36", :color :blue, :size :medium}]
 [#:inv{:sku "SKU-40", :color :blue, :size :large}]
 [#:inv{:sku "SKU-44", :color :blue, :size :xlarge}]
 [#:inv{:sku "SKU-48", :color :yellow, :size :small}]
 [#:inv{:sku "SKU-52", :color :yellow, :size :medium}]
 [#:inv{:sku "SKU-56", :color :yellow, :size :large}]
 [#:inv{:sku "SKU-60", :color :yellow, :size :xlarge}]]
```

### Test Lambda Entry Points at the REPL

A lambda entry point is just a function with a lambda-compatible signature that receives JSON input, returning an arbitrary string of output.

The lambda entry points are in a separate `lambdas` namespace, so that you can easily notice the difference between domain functions and lambda data marshaling.

[Review the lambda entry points](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter/lambdas.clj), and then try them from the REPL.

```clojure
(lambdas/get-schema nil)
```

```clojure
#db{:id 39, :ident :fressian/tag, :valueType :db.type/keyword, :cardinality :db.cardinality/one, :doc "Keyword-valued attribute of a value type that specifies the underlying fressian type used for serialization."}
#db{:id 73, :ident :inv/sku, :valueType :db.type/string, :cardinality :db.cardinality/one, :unique #db{:id 38, :ident :db.unique/identity}}
#db{:id 74, :ident :inv/color, :valueType :db.type/keyword, :cardinality :db.cardinality/one}
#db{:id 75, :ident :inv/size, :valueType :db.type/keyword, :cardinality :db.cardinality/one}
#db{:id 76, :ident :inv/type, :valueType :db.type/keyword, :cardinality :db.cardinality/one}
#db{:id 77, :ident :order/items, :valueType :db.type/ref, :cardinality :db.cardinality/many, :isComponent true}
#db{:id 78, :ident :item/id, :valueType :db.type/ref, :cardinality :db.cardinality/one}
#db{:id 79, :ident :item/count, :valueType :db.type/long, :cardinality :db.cardinality/one}
#db{:id 80, :ident :inv/count, :valueType :db.type/long, :cardinality :db.cardinality/one}
```

### Test HTTP Entry Points at the REPL

An HTTP entry point is just a function with an [HTTP-compatible signature](../02-ions-reference/ions-reference.md#http-direct-entry-point) that receives input and output maps that describe web requests and responses.

The HTTP entry points are in a separate `http` namespace. Review the HTTP entry points, and then try them from the REPL.

```clojure
(http/get-items-by-type {:body (s-edn/input-stream :shirt)})
```

```clojure
{:status 200, :headers {"Content-Type" "application/edn"}, :body "[[#:inv{:sku \"SKU-0\", :size :small, :color :red}]
 [#:inv{:sku \"SKU-4\", :size :medium, :color :red}]
 [#:inv{:sku \"SKU-8\", :size :large, :color :red}]
 [#:inv{:sku \"SKU-12\", :size :xlarge, :color :red}]
 [#:inv{:sku \"SKU-16\", :size :small, :color :green}]
 [#:inv{:sku \"SKU-20\", :size :medium, :color :green}]
 [#:inv{:sku \"SKU-24\", :size :large, :color :green}]
 [#:inv{:sku \"SKU-28\", :size :xlarge, :color :green}]
 [#:inv{:sku \"SKU-32\", :size :small, :color :blue}]
 [#:inv{:sku \"SKU-36\", :size :medium, :color :blue}]
 [#:inv{:sku \"SKU-40\", :size :large, :color :blue}]
 [#:inv{:sku \"SKU-44\", :size :xlarge, :color :blue}]
 [#:inv{:sku \"SKU-48\", :size :small, :color :yellow}]
 [#:inv{:sku \"SKU-52\", :size :medium, :color :yellow}]
 [#:inv{:sku \"SKU-56\", :size :large, :color :yellow}]
 [#:inv{:sku \"SKU-60\", :size :xlarge, :color :yellow}]]"}
```

Note that the REPL tests provide only the parts of the request map that are needed. This optionality is a benefit over more static systems that might require mocking and stubbing.

Now you are ready to [push and deploy](../06-push-and-deploy/push-and-deploy.md) your ion to Datomic Cloud.

# Entry Points

There are [many entry points for ions](../02-ions-reference/ions-reference.md#ion-entry-points). This section covers:

- [Lambda invocation](#invoke-a-lambda)
- [HTTP direct](#http-direct)
- [Attribute predicates](../../06-reference/01-schema/01-schema-reference/schema-reference.md#attribute-predicates)

## Invoke a Lambda

Lambda entry points can be invoked from the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/lambda/index.html). First, call `get-schema`. In the command below, replace `$(GROUP)` with the [name of your compute group](../../05-operation/02-cloud/11-how-to/how-to.md#find-compute-group-name):

```sh
aws lambda invoke --function-name $(GROUP)-get-schema --payload '' /dev/stdout
```

This may return pending while AWS [creates or configures external resources](https://aws.amazon.com/blogs/compute/tracking-the-state-of-lambda-functions/).

On success, this call will print your lambda's response, plus some AWS CLI status information.

```clojure
(#:db{:id 39,
      :ident :fressian/tag,
      :valueType :db.type/keyword,
      :cardinality :db.cardinality/one,
      :doc
      "Keyword-valued attribute of a value type that specifies the underlying fressian type used for serialization."}
 ;; more schema elided
 #:db{:id 80,
      :ident :inv/count,
      :valueType :db.type/long,
      :cardinality :db.cardinality/one})
{
    "StatusCode": 200,
    "ExecutedVersion": "$LATEST"
}
```

On your first invocation, this call may take a while due to lambda cold start. Outside of dev, [most lambda invocations are warm](https://blog.symphonia.io/learning-lambda-part-8-addfab6b460d), so try calling the function a second time to see typical production performance.

To see a function that takes an argument, try `get-items-by-type`.

> If `aws --version` returns version 2, then add the flag `--cli-binary-format raw-in-base64-out` to the command:
>
> ```sh
> aws lambda invoke --function-name $(GROUP)-get-items-by-type --payload '"shirt"' /dev/stdout
> ```

On success, this will return details about shirts:

```clojure
[[#:inv{:sku "SKU-28", :size :xlarge, :color :green}]
 ;; more shirt details
 [#:inv{:sku "SKU-56", :size :large, :color :yellow}]]
{
    "StatusCode": 200,
    "ExecutedVersion": "$LATEST"
}
```

## Add an Attribute Predicate

You can use ions to deploy functions for use as [attribute predicates](../../06-reference/01-schema/01-schema-reference/schema-reference.md#attribute-predicates), [entity predicates](../../06-reference/01-schema/01-schema-reference/schema-reference.md#entity-predicates), [transaction functions](../../06-reference/02-transactions/04-transaction-functions/transaction-functions.md#writing-transaction-functions), or [query functions](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#calling-clojure-functions).

When you deployed the tutorial application, your [ion-config.edn file](https://github.com/Datomic/ion-starter/blob/master/resources/datomic/ion-config.edn) allowed a [valid-sku? function](https://github.com/Datomic/ion-starter/blob/master/src/datomic/ion/starter/attributes.clj) suitable for an attribute predicate.

```clojure
(defn valid-sku?
  [s]
  (boolean (re-matches #"SKU-(\d+)" s)))
```

First test this function at the REPL:

```clojure
(load-file "siderail/user.repl")

;; try the fn at the REPL
(attrs/valid-sku? "SKU-112")
```

```clojure
true
```

```clojure
(attrs/valid-sku? "SKU-1B")
```

```clojure
false
```

Then you can install `valid-sku?` as a predicate for `:inv/sku`:

```clojure
(def conn (starter/get-connection))

(def tx [{:db/ident :inv/sku
          :db.attr/preds 'datomic.ion.starter.attributes/valid-sku?}])
(d/transact conn {:tx-data tx})
```

After installing the function, you can use a `with-db` to prove that the predicate is in effect:

```clojure
(def with-db (d/with-db conn))
(d/with with-db {:tx-data [{:db/id "should-not-work"
                            :inv/sku "not-a-sku"}]})
```

```text
Entity -9223301668109598141 attribute :inv/sku value not-a-sku failed pred datomic.ion.starter.attributes/valid-sku?
```

## HTTP Direct

Invoking an ion via HTTP Direct is a simple HTTP request.

Find your `IonApiGatewayEndpoint` in your compute group's [template outputs](../../05-operation/02-cloud/11-how-to/how-to.md#find-template-outputs).

Since HTTP Direct is a simple HTTP request, it can be invoked from any platform that supports such requests. Try entering your `IonApiGatewayEndpoint` and data of `:hat` below.

| Field | Value |
|---|---|
| `IonApiGatewayEndpoint` | `your-endpoint-here` |
| `Payload` | `:hat` |

`[Submit]`

On success, this will return an EDN representation of inventory data for a particular type.

> The above [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) request would normally require that [CORS for your API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-cors.html) is setup with an allowed Origin of "https://docs.datomic.com". The demo above sends the request to an ion we've set up with the appropriate CORS headers. This intermediate Ion requests to your supplied endpoint and returns the result. If your API Gateway was set up with the appropriate CORS response then you could make the request directly to your `IonApiGatewayEndpoint` endpoint.

The request below is equivalent to invoking your ion through HTTP Direct with `curl`:

```sh
curl https://$(IonApiGatewayEndpoint) -d :hat
```

```clojure
#{{:color :red, :type :hat, :size :medium, :sku "SKU-7"}
  ...
  {:color :blue, :type :hat, :size :small, :sku "SKU-35"}}
```

Now [delete your system and conclude this tutorial](../08-conclusion/conclusion.md).

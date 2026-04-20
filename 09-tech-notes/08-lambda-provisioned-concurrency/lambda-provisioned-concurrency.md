# Lambda Provisioned Concurrency

Configuring your Datomic [ion Lambda](../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#lambdas) with
[provisioned concurrency](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html#configuration-concurrency-provisioned)
can alleviate Lambda cold-start issues when the Ion is infrequently invoked.

To configure provisioned concurrency for an ion lambda:

- [Deploy a lambda ion](../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#deploy)
- [Publish a version of the lambda](https://docs.aws.amazon.com/lambda/latest/dg/API_PublishVersion.html)
- (Optional) [Provide an alias for the published version](https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html)
- [Configure provisioned concurrency for the lambda version or alias](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html#configuration-concurrency-provisioned)

## Example

The following example uses the [aws-api](https://github.com/cognitect-labs/aws-api) to publish a new version of a lambda function,
create an [alias](https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html) for that version,
and configure provisioned concurrency for it. Run it in an environment with
proper AWS credentials to create and modify AWS lambda resources. The example can be run any time after an
[ion deploy](../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#deploy) to ensure that the latest lambda is published and configured with
provisioned concurrency.

`deps.edn`:

```clojure
{:deps {com.cognitect.aws/api       {:mvn/version "0.8.474"}
        com.cognitect.aws/endpoints {:mvn/version "1.1.11.842"}
        com.cognitect.aws/lambda    {:mvn/version "809.2.734.0"}}}
```

```clojure
;; sample code

(require [cognitect.aws.client.api :as aws])

(defn aws-invoke!
  "Calls aws/invoke on the provided client and arg-map.
  Throws if the response map includes an anomaly, returns
  the map otherwise."
  [client arg-map]
  (let [resp (aws/invoke client arg-map)]
    (if (:cognitect.anomalies/category resp)
      (throw (ex-info "Anomaly returned from aws/invoke" resp))
      resp)))

(defn publish-and-set-concurrency!
  "Publishes latest lambda, updates alias, and
  sets provisioned concurrency.
  Requires the lambda name and an alias name as strings and
  the desired provisioned concurrency as an int.
  Returns the result map from final call to invoke
  operation for :PutProvisionedConcurrencyConfig"
  [{:keys [fn-name fn-alias concurrency]}]
  (let [lambda-client (aws/client {:api :lambda})
        publish-resp (aws-invoke! lambda-client {:op :PublishVersion
                                                 :request {:FunctionName fn-name}})
        alias-info (aws/invoke lambda-client {:op :GetAlias
                                              :request {:FunctionName fn-name
                                                        :Name fn-alias}})
        alias-resp (if (= (:cognitect.anomalies/category alias-info)
                          :cognitect.anomalies/not-found)
                     (aws-invoke! lambda-client {:op :CreateAlias
                                                 :request {:FunctionName fn-name
                                                           :Name fn-alias
                                                           :FunctionVersion (:Version publish-resp)}})
                     (aws-invoke! lambda-client {:op :UpdateAlias
                                                 :request {:FunctionName fn-name
                                                           :Name fn-alias
                                                           :FunctionVersion (:Version publish-resp)}}))]
    (aws-invoke! lambda-client {:op :PutProvisionedConcurrencyConfig
                                :request {:FunctionName fn-name
                                          :Qualifier fn-alias
                                          :ProvisionedConcurrentExecutions concurrency}})))

; Usage: (publish-and-set-concurrency! {:fn-name "my-lambda-name"
;                                       :fn-alias "my-alias"
;                                       :concurrency 1})
```

When using the newly configured lambda, for example as a target of [a lambda](../../07-datomic-cloud-ions/07-entry-points/entry-points.md#invoke-a-lambda),
specify the alias as the target:

`my-lambda-name:my-alias`

> AWS charges apply for lambda-provisioned concurrency. Check the
> [lambda pricing guide](https://aws.amazon.com/lambda/pricing/#Provisioned_Concurrency_Pricing) for more information.

## Reference

[AWS blog announcing provisioned concurrency](https://aws.amazon.com/blogs/aws/new-provisioned-concurrency-for-lambda-functions/).

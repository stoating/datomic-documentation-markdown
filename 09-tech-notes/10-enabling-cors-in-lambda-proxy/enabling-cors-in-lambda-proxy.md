# Enabling CORS in a Lambda Proxy

## The Problem

Preflight `OPTIONS` requests are blocked by the `CORS` policy when using Cognito to authorize requests to an API Gateway HTTP direct `$default`.

## The Solution

- Go to the AWS [API Gateway console](https://console.aws.amazon.com/apigateway/home).
- Select your HTTP direct API.
- Attach your authorizer to your `$default` route and follow the steps to set it up.
- Click "Routes".
- Click "Create".
- Create a new `OPTIONS` route with a path of `/{proxy+}`.
- Attach your HTTP Direct (port 8184) integration to this path.
- Click "CORS".
- Click "Configure".
- Configure as required by your application.

### Adding CORS Headers in Your App

With the above change, requests will now be passed to your application which will be responsible for adding the desired CORS headers. A maximally permissive set of headers is provided here as a reference. You may adjust based on your specific needs:

```clojure
(def cors-headers {"Access-Control-Allow-Origin" "*"
                   "Access-Control-Allow-Methods" "GET, PUT, PATCH, POST, DELETE, OPTIONS"
                   "Access-Control-Allow-Headers" "Authorization, Content-Type"})
```

## The Solution (Legacy)

> This section only applies to [Datomic 781-9041](../../11-releases/02-datomic-cloud/02-cloud-change-log/cloud-change-log.md#20210302-781-9041-compute-update) and lower.

Add an unauthenticated lambda proxy OPTIONS method to your API gateway. Then add the appropriate CORS headers to the request response from within your application.

### Adding The Method

- Go to the AWS [API gateway console](https://console.aws.amazon.com/apigateway/home).
- Under the "Actions" dropdown, choose "Create method".
- Select "OPTIONS" from the drop-down.
- Click the checkmark next to the dropdown box.
- In "Lambda function", choose the region and lambda function that proxies to your app.
- Click "Save".
- Under the Actions dropdown, select "Deploy API" and use the next window to deploy your API.

### Adding CORS Headers in Your App

With the above change, requests will now be passed to your application which will be responsible for adding the desired CORS headers. A maximally permissive set of headers is provided here as a reference. You may adjust based on your specific needs:

```clojure
(def cors-headers {"Access-Control-Allow-Origin" "*"
                   "Access-Control-Allow-Methods" "GET, PUT, PATCH, POST, DELETE, OPTIONS"
                   "Access-Control-Allow-Headers" "Authorization, Content-Type"})
```

# Ions Cleanup Tutorial

## Cleanup

If you want to [delete your Datomic Cloud system](../../05-operation/02-cloud/15-deleting/deleting.md) after you've deployed an Ion, first deploy an ion with a `resources/datomic/ion-config.edn` that consists of a map of the `:app-name` of your application.

Change your `resources/datomic/ion-config.edn` to the following map, and replace `:app-name` with your application name:

```clojure
{:app-name "app-name"}
```

Now [push](../06-push-and-deploy/push-and-deploy.md#push) and [deploy](../06-push-and-deploy/push-and-deploy.md#deploy).

Once this ion-config.edn is deployed, the Datomic Cloud system can [be deleted](../../05-operation/02-cloud/15-deleting/deleting.md).

## Conclusion

In this tutorial, you have seen how ions can take ordinary Clojure functions, and install them in Datomic Cloud where they have in-memory access to Datomic data. You have used ions to deliver:

- [A data-driven application via AWS Lambda](../07-entry-points/entry-points.md#invoke-a-lambda)
- [Attribute predicates](../07-entry-points/entry-points.md#add-an-attribute-predicate)
- [A web service via API Gateway](../07-entry-points/entry-points.md#http-direct)

There is a lot more to explore! Here are some things to try:

- Use ions to implement a lambda that [handles events](https://docs.aws.amazon.com/lambda/latest/dg/lambda-services.html) from other AWS services. Check the [ion-event-example](https://github.com/Datomic/ion-event-example) project.
- Instead of wiring a web handler to a single function, use a routing library such as [Compojure](https://jacobobryant.com/post/2019/ion/) or [pedestal](https://github.com/pedestal/pedestal.ions).
- Learn to [Authenticate with Cognito](../09-authentication-with-cognito/authentication-with-cognito.md).

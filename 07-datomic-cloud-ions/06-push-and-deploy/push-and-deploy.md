# Push and Deploy

## Push

The *push* operation creates an application revision in S3 and AWS CodeDeploy that can then be deployed multiple times to compute groups with the same application name.

### Configure Entry Points

When you push an application revision, the `resources/datomic/ion-config.edn` file specifies the application's entry points:

- The `:lambda` section specifies functions that will be callable via AWS Lambda
- The `:http-direct` section specifies functions that will be callable via HTTP
- The `:allow` section specifies functions that will be callable from inside Datomic transactions or queries

For this tutorial, the entry points are [already specified](https://github.com/Datomic/ion-starter/blob/master/resources/datomic/ion-config.edn). Review them before you push.

### Commit Your Changes

By default, *push* requires a clean git repository, and uses the git SHA to name the application revision in AWS. Such a push is *reproducible*. To prepare for a reproducible commit, `git add` files that are part of your application, git ignore any extraneous files, and `git commit`. If you are precisely following the tutorial, you have only changed resource files to set your application name and configured a client.

```sh
git add resources
git commit -m 'application name and client config'
```

To double-check that you have a clean commit, run `git status`:

```sh
git status
```

```text
... details elided ...
nothing to commit, working tree clean
```

### Push a Revision

Now you are ready to push an application revision. The [push command](../02-ions-reference/ions-reference.md#push) looks like:

```sh
clojure -A:ion-dev '{:op :push}'
```

This copies files to S3, so you will need S3 put permissions for the push operation. If your ambient credentials do not give you permission to the system that you're working with, you can specify a credentials profile to use by adding `:creds-profile "creds"` to the map.

The [push](../02-ions-reference/ions-reference.md#push) operation will report progress on the console. If it succeeds, it will print an EDN map that includes a `:deploy-command`.

Congratulations, you have pushed an app!

If *push* fails, don't worry. Use the [push troubleshooting](../../05-operation/02-cloud/13-cloud-troubleshooting/cloud-troubleshooting.md#troubleshooting-ion-push) guide to diagnose and fix any problems you encounter.

## Deploy

The [deploy](../02-ions-reference/ions-reference.md#deploy) operation installs your [pushed](#push) application on a Datomic compute group which you specify.

Let's deploy the revision you just pushed. The output of a successful push will include some important pieces of information:

- `:rev` or `:uname` – the name of the git SHA or [unreproducible push name](../02-ions-reference/ions-reference.md#unreproducible-push)
- `:deploy-groups` – a list of compute groups with the same `:app-name`
- `:deploy-command` – an example deploy command. This may require unescaping backslashes

The [format for a deploy command](../02-ions-reference/ions-reference.md#deploy) is:

```sh
clojure -A:ion-dev '{:op :deploy :rev $(REV) :group $(GROUP)}'
```

If you've been following this tutorial then the `:deploy-command` will have your deploy command with the `:rev` and `:group` filled in.

On success, a *deploy* will print an edn map that includes a `:status-command`.

Congratulations, your app revision is now deploying!

If *deploy* fails, then use the [deploy troubleshooting](../../05-operation/02-cloud/13-cloud-troubleshooting/cloud-troubleshooting.md#troubleshooting-ion-deploy) guide to diagnose and fix any problems.

## Monitor Your Deployment

The *deploy* operation does the minimal work necessary to ensure that your application revision is running on all your compute nodes. This can take anywhere from a few seconds to several minutes.

The output of an ion deploy includes the `:status-command` to monitor your deployment. Copy the value of this key, remove any escaping, and use it to monitor your ion deploy.

The format to monitor [deploy-status](../02-ions-reference/ions-reference.md#deploy-status) is:

```sh
clojure -A:ion-dev '{:op :deploy-status :execution-arn $(EXECUTION_ARN)}'
```

`:execution-arn` is output by a successful [deploy](#deploy) command.

Deploy Status returns the [Step Functions reference](https://docs.aws.amazon.com/step-functions/latest/apireference/API_DescribeExecution.html#StepFunctions-DescribeExecution-response-status) of the overall ion deploy and the code deploy as a map.

```clojure
{:deploy-status "SUCCEEDED", :code-deploy-status "SUCCEEDED"}
```

If your `deploy-status` doesn't reach `SUCCEEDED`, then use the [deploy-status troubleshooting guide](../../05-operation/02-cloud/13-cloud-troubleshooting/cloud-troubleshooting.md#troubleshooting-ion-deploy-status) to diagnose and fix any problems.

Once your ion is successfully deployed, explore the [entry points](../07-entry-points/../07-entry-points/entry-points.md) to your ion.

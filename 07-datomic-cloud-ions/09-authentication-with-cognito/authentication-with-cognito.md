# Authentication with Cognito

Authentication can be managed with [AWS Cognito](https://console.aws.amazon.com/cognito/home) and invoked [utilizing the AWS SDK](https://aws.amazon.com/tools/).

These steps will set up an **unauthenticated** identity pool. Implementing [an authentication flow](https://docs.aws.amazon.com/cognito/latest/developerguide/authentication-flow.html) can be done with a setup similar to these instructions.

- Go to [Cognito](https://console.aws.amazon.com/cognito/home)
  - If this is your first time, click "Manage identity pools"
- Click "Create new identity pool"
- Give your identity pool a name
- Click the "Enable access to unauthenticated identities" checkbox
- Create pool
- Select "View Details"
- Edit the **unauthenticated** policy for a new IAM role with the policy below
- Replace the `arn:` values in the "Resource" array with [the ARNs of your lambdas](https://console.aws.amazon.com/lambda/home)
  - These will be in the form of `app-name-compute-function-name` i.e. "cognito-tutorial-compute-get-items-by-type"
  - Click the Lambda name and copy the `Function ARN` on the next page.
- Click "Allow"
- Save the identity pool ID

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "mobileanalytics:PutEvents",
        "cognito-sync:*"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "lambda:InvokeFunction",
        "lambda:InvokeAsync"
      ],
      "Resource": [
        "arn:my-arns-here"
      ]
    }
  ]
}
```

> The above policy is more permissive than most applications will require. [Limit the policy](https://docs.aws.amazon.com/cognito/latest/developerguide/iam-roles.html) to only what is necessary.

## Test Your Lambda

To test your Cognito setup:

- Supply your Cognito identity pool ID and region.
- Press submit. The returned payload will be displayed below.

| Field | Value |
|---|---|
| `Region` | `us-east-1` |
| `Identity Pool` | `us-east-1:c62bfc0a-a57f-4a06-95ca-6828638175a0` |
| `Function Name` | `ion-web-compute-get-items-by-type` |
| `Payload` | `"shirt"` |

`[Submit]`

`Lambda Results displayed here`

## AWS Javascript SDK Example

This example utilizes the [AWS SDK for Javascript](https://aws.amazon.com/sdk-for-javascript/) to invoke the supplied function.

The [AWS SDK](https://aws.amazon.com/getting-started/tools-sdks/) is officially supported by a variety of programming languages.

A simple non-parameterized example:

```html
<script src="https://sdk.amazonaws.com/js/aws-sdk-2.854.0.min.js"></script>
```

```javascript
function invoke_lambda() {
    AWS.config.region = 'region'
    AWS.config.credentials = new AWS.CognitoIdentityCredentials({
        IdentityPoolId: "identity-pool-value",
    });
    lambda = new AWS.Lambda({
        region: "region",
        apiVersion: '2015-03-31'
    });
    var pullParams = {
        FunctionName: "function-name",
        InvocationType: 'RequestResponse',
        LogType: 'None',
        Payload: "payload"
    };
    lambda.invoke(pullParams, function(err, data) {
        {
            if (err) {
              // handle error
            } else {
              // handle data.Payload 
            }
        }
    })
}
```

- Change the placeholders above to the appropriate values for:
  - `AWS.config.region`
  - `IdentityPoolId`
  - `region`
  - `FunctionName`
  - `Payload`
- Then call `invoke_lambda()`

# Customizing API Gateways

By default, every Datomic [compute group](../01-cloud-architecture/cloud-architecture.md#compute-groups) manages two API Gateways: one for
client access, and another for [ion applications](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md). This page covers
circumstances where you may want to go beyond Datomic's built-in API
Gateway support:

- [Creating a custom domain for Datomic's API Gateways](#creating-a-custom-domain)
- [Creating your own API Gateways](#creating-your-own-api-gateway)

## Creating a Custom Domain

The API Gateways managed by Datomic have generated endpoint names of the form
[https://{api-id}.execute-api.{region}.amazonaws.com/](https://{api-id}.execute-api.{region}.amazonaws.com/). If you want
friendly custom domains in addition to these generated names, you can
configure them either through [Route53](#route53) or [your own DNS servers/registrar](#your-dns-servers).

Both methods described use [AWS Certificate Manager](https://console.aws.amazon.com/acm/home).

### Route53

> These instructions assume that you're using a subdomain.

The AWS Certificate Manager instructions may differ based on your needs. These instructions provide a basic overview of how to set up a custom domain name.

#### AWS Certificate Manager

- Go to [AWS Certificate Manager](https://console.aws.amazon.com/acm/home)
- Possibly click Provision Certificates
- Request a certificate
- Select Request a public certificate
- Click "Request a certificate"
- Enter the domain name that you wish to use
- Click "Next"
- Select the DNS validation method that you wish to use
- Click "Next"
- Add any tags that you would like for easier identification of this Certificate later
- Click "Review"
- Review your selections
- Click "Confirm and request"
- If you used DNS Validation:
  - Click the down arrow next to the domain name in the "Domain" box
  - Click "Create a record in Route 53"
  - Review the information on the next screen.
  - Click "Create"
- Once the Certificate's status in ACM is completed, proceed to the next step

#### API Gateway

- Go to [API Gateway](https://console.aws.amazon.com/apigateway/main/apis)
- Select the API that you want to connect a custom domain name to
- Click "Custom domain names"
- Click "Create"
- Enter the desired domain name
- Leave the defaults
  - TLS 1.2
  - Mutual authentication off
  - Endpoint type: regional
- Select the ACM Certificate that you created earlier or the appropriate existing ACM Certificate
- Add any tags that you would like for easier identification of this custom domain later
- Click "Create Domain Name"
- On the following page for the newly created domain name, click the "API mappings" tab
  - Click "Configure API mappings"
  - Click "Add new mapping"
  - Select the API that you want to connect a custom domain name to
  - Select the appropriate stage, likely `$default`
  - Click "Save"
- Under "Configurations" copy and save the API Gateway domain name for the next step

#### Route53

- Go to [Route53](https://console.aws.amazon.com/route53/v2/home)
- Click "Hosted Zones"
- Click the domain name that you are using
- Create record
- Record type "A" or "AAA"
- Record name is the subdomain used previously
- Route traffic to "Alias to API Gateway API"
- Choose the appropriate region
- Choose the endpoint matching the name that you saved earlier
- Click "Create Records"

After the DNS propagates, you will be able to visit a domain connected to a Client API Gateway and get a response similar to:

```clojure
{:s3-auth-path "<stack-name>-storagef7f305e7-z5ezcdtid-s3datomic-2n2m24rzlu6al"}
```

### Your DNS Servers

> These instructions assume that you're using a subdomain.

The AWS Certificate Manager instructions may differ based on your needs. These instructions provide a basic overview of how to set up a custom domain name.

#### AWS Certificate Manager

- Go to [AWS Certificate Manager](https://console.aws.amazon.com/acm/home)
- Possibly click "Provision certificates"
- Request a certificate
- Select Request a public certificate
- Click "Request a certificate"
- Enter the domain name that you wish to use
- Click "Next"
- Select the DNS validation method that you wish to use
- Click "Next"
- Add any tags that you would like for easier identification of this certificate later
- Click "Review"
- Review your selections
- Click "Confirm and request"

#### API Gateway

- Go to [API Gateway](https://console.aws.amazon.com/apigateway/main/apis)
- Select the API that you want to connect a custom domain name to
- Click "Custom domain names"
- Click "Create"
- Enter the desired domain name
- Leave the defaults
  - TLS 1.2
  - Mutual authentication off
  - Endpoint type: regional
- Select the ACM Certificate that you created earlier or the appropriate existing ACM Certificate
- Add any tags that you would like for easier identification of this custom domain later
- Click "Create domain name"
- On the following page for the newly created domain name, click the "API mappings" tab
  - Click "Configure API mappings"
  - Click "Add new mapping"
  - Select the API that you want to connect a custom domain name to
  - Select the appropriate stage, likely `$default`
  - Click "Save"

#### DNS

- Go to where you manage your DNS
- Subdomain - if you used a subdomain:
  - Create a new CNAME record
  - Host is the API gateway domain name listed under your custom domain names endpoint configuration
  - Value is the API gateway endpoint URL

After the DNS propagates, you will be able to visit a domain connected to a client API gateway and get a response similar to:

```clojure
{:s3-auth-path "<stack-name>-storagef7f305e7-z5ezcdtid-s3datomic-2n2m24rzlu6al"}
```

Ion API gateway domains will need to be verified using your HTTP direct invoke method.

## Creating Your Own API Gateway

Datomic's built-in API Gateways will cover the majority of use cases,
but it is possible to create your own API Gateways in addition to, or
instead of, the API Gateways managed by Datomic. The instructions
below cover creating and using your own API Gateways, both for Datomic
clients and for ion applications.

### Find Load Balancer Name

- Go to [Cloudformation](https://console.aws.amazon.com/cloudformation/)
- Select your master stack, primary compute group, or query group that you want to add an endpoint to
- Click the "Outputs" tab
- Search for "LoadbalancerName" and take note of the associated value

### Create API Gateway

- Go to the [API Gateway](https://console.aws.amazon.com/apigateway/main/apis)
- Click "Create API"
- Click "Build" under "HTTP API"
- API name - "datomic-<system-name>-Client" or "datomic-<system-name>-HTTP-Direct" is suggested, depending on the gateway type
- Click "Next" on the configure routes page
- Use the default Stage name value of `$default`. Click "Next"
- Do not try to edit integrations at this time
- Click "Create"

### Create a Route

- On the page for your newly created API, click "Routes"
- Path - `$default`
  - A Client Access gateway must use a `$default` path. HTTP Direct access can be set up according to your needs

### Create an Integration

- Click "Integrations"
- Select your `default` path
- Click "Create and attach an integration"
- Integration type: private resource
- Selection method: select manually
- Target service: ALB/NLB
- Load balancer: use the value previously located in CloudFormation
- Listener
  - TCP **8182** for client access
  - TCP **8184** for HTTP direct access
- VPC link: select the name of your system
- Click "Create"

### Using Your Client Access API Gateway

- Go to [API gateway](https://console.aws.amazon.com/apigateway/main/apis)
- Select the API that you created for client

Your endpoint is now displayed in the "Stages" section of your API's page in [API Gateway](https://console.aws.amazon.com/apigateway/main/apis). Curl or view the link in your web browser. You should see text that looks like `{:s3-auth-path "my-datomic-systems3datomic-m62qe9be3uwp"}`. The exact value will differ for you.

### Using Your HTTP Direct Access API Gateway

The HTTP Direct endpoint [is used to access your ions over the internet](../../../07-datomic-cloud-ions/07-entry-points/entry-points.md#http-direct).

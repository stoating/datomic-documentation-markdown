# Intra and Inter VPC Access

For most applications, the preferred way to access Datomic is via the [built-in API gateways](../01-cloud-architecture/cloud-architecture.md#api-gateways), which includes powerful features for traffic management and authn/authz.

This page describes alternatives for situations where you *don't* want an API Gateway. If you want callers to connect to a private endpoint instead, you can connect directly to the load balancer for a compute group. There are two alternatives:

- **Intra-VPC**: if you launch AWS resources [in Datomic's VPC](#intra-vpc), they will automatically be able to connect to Datomic.
- **Inter-VPC**: callers from other VPCs require additional setup. The specific instructions vary depending on the type of load balancer Datomic provisions for your compute group, and are covered under [ALB](#vpc-peering) and [NLB](#vpc-endpoint) instructions below.

Each of these alternatives is described in more detail below. Throughout this page, the following metavariables are used:

- *SystemName* is the Datomic system name.
- *GroupName* is a Datomic compute group name. For the primary compute group, this is the *SystemName*. For a query group, this is the name of the query group.
- *Region* is the AWS region in which the Datomic system is running.

## Intra-VPC Access

The [storage stack](../02-start-a-system/start-a-system.md#create-a-storage-stack) creates a [Virtual Private Cloud](https://aws.amazon.com/vpc/), named *datomic-$(SystemName)* in which to run a Datomic system. Inside this VPC, the stack creates 3 subnets named *datomic-$(SystemName)-subnet-0*, *datomic-$(SystemName)-subnet-1*, and *datomic-$(SystemName)-subnet-2*.

Launch your calling application instances into any of these subnets, which are preconfigured for access to the Datomic system.

## Inter-VPC Access

To configure inter-VPC access, first determine whether your Datomic system uses an ALB or an NLB:

- Versions of Datomic from 884-9095 (2021/07/13) forward use an ALB.
- Versions of Datomic from 732-8993 (2020/11/30) to 781-9041 (2021/03/02) use an NLB.
- Versions of Datomic older than 732-8993 (2020/11/30) are no longer supported. Upgrade before configuring an Inter-VPC connection.

Then follow the directions below as appropriate for your load balancer.

### Inter-VPC Access with VPC Peering (ALB only)

To connect callers from an external VPC to a Datomic compute group ALB, create a

[VPC Peering Connection](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/working-with-vpc-peering.html) between the calling VPC and Datomic VPC.

Check the AWS documentation for:

- [Creating and accepting a VPC peering connection](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/create-vpc-peering-connection.html#create-vpc-peering-connection-local)
- [Updating the route tables](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/vpc-peering-routing.html)
- [Configuring securty group ingress](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html)

Datomic nodes run in a security group with tag `Name=(System)-nodes`.

To allow applications in your existing VPC referring to the Datomic system entry point using its DNS name, `entry-http.$(SystemName).$(Region).datomic.net:8182`, you must also associate your calling VPC with the Datomic system's Route 53 hosted zone:

[Associating a VPC with a Private Hosted Zone](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zone-private-associate-vpcs.html)

Associate your existing VPC with the Datomic system Route 53 Hosted Zone, named `$(SystemName).$(Region).datomic.net`. This allows the Datomic system VPC to handle private DNS resolution of the datomic.net domain for your VPC.

### Inter-VPC Access with VPC Endpoint (NLB only)

To connect callers from an external VPC to a Datomic compute group NLB, check [VPC Endpoint](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-endpoints.html).

- Your application VPC has to be in the same AWS region as your Datomic Cloud system
- Your VPC has to contain at least one subnet in the same Availability Zone (AZ) as one of the Datomic subnets

#### Verifying Subnet AZs

Find your Datomic System subnets and your application VPC subnets in the [subnet list](https://console.aws.amazon.com/vpc/home#subnets:) of the VPC Dashboard, taking note of the availability zones.

If at least one of your application VPC subnets is in the same AZ as one of the Datomic system subnets, proceed to [create your VPC endpoint](#create-endpoint).

If your application VPC does not contain any AZ-matching subnets, you will need to [create an additional subnet](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/working-with-vpcs.html#AddaSubnet) (or subnets) in your VPC in one of the AZs used by your Datomic system subnets.

#### Creating VPC Service and Endpoint

- In the [CloudFormation Console](https://console.aws.amazon.com/cloudformation/home?#/stacks?filter=active&tab=outputs) browse to the Output tab of your Datomic compute or query group stack
- Find and record the Load Balancer Name reported under the key "LoadBalancerName"
- Open the [Endpoint Services Console](https://console.aws.amazon.com/vpc/home?#EndpointServices:sort=serviceId)
- Click "Create endpoint service"
- Choose the network load balancer corresponding to the name you recorded from the CloudFormation output
- Click "Create service"
- Record the endpoint service address of the new service
- Open the [VPC Endpoints Console](https://console.aws.amazon.com/vpc/home?#Endpoints:sort=vpcEndpointId)
- Click "Create endpoint"
- Select "Find service by name"
- Paste the VPC Endpoint Service address recorded in the prior step in the "Service Name" field
- Click "Verify" to ensure the address is correct
- Click "Create endpoint"

#### Using the VPC Endpoint

Datomic clients must use the VPC Endpoint DNS (or Route53 entry if you created one) and port 8182 for the `:endpoint` parameter in your Datomic [client configuration](../../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md#client) when connecting from your VPC:

```clojure
(def cfg {:server-type :ion
          :region "<your AWS Region>" ;; e.g. us-east-1
          :system "<system-name>"
          :endpoint "http://<VpcEndpointDns>:8182"})
```

The endpoint DNS name can be found in the outputs of the VPC Endpoint CloudFormation Stack under the `VpcEndpointDns` key.

#### Deleting VPC Endpoint and Service

To delete an endpoint and endpoint service that you no longer need:

- First, delete the endpoint that is using the Endpoint Service. Generally, this will be the API gateway entry you are using for your lambda ions
- In the [CloudFormation Console](https://console.aws.amazon.com/cloudformation/) browse to the output tab of your Datomic compute or query group stack
- Find and record the service endpoint ID under the key "*VpcEndpointServiceId*"
- Open the [endpoint service page](https://console.aws.amazon.com/vpc/home?#EndpointServices:sort=serviceId)
- Find the Datomic endpoint service via the ID you recorded from the CloudFormation output
- Highlight the service, click "Action" and "Delete service"

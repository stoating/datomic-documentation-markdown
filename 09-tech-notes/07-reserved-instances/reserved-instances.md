# Reserved Instances

The ongoing cost of running Datomic EC2 instances can be reduced by purchasing AWS [Reserved Instances](https://aws.amazon.com/ec2/pricing/reserved-instances/) (RIs).

Once you have reviewed [growing your system](../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md), you can purchase Reserved Instances for the [EC2 instance types](../../05-operation/02-cloud/03-growing-your-system/growing-your-system.md#instance-sizes) used in your system. AWS will automatically apply the purchased RI discount to your infrastructure (EC2) cost for any instances matching the purchased RI attributes.

## Determining Instance Sizes

Standard RIs are purchased for a specific EC2 instance type. You can determine the type(s) and quantities of EC2 instances your Datomic system uses by [searching for the keyword 'datomic' in your EC2 Console](https://console.aws.amazon.com/ec2/v2/home#Instances:search=datomic;sort=desc:launchTime):

[![instanceTypesRI](https://docs.datomic.com/images/instanceTypesRI.png)](https://docs.datomic.com/images/instanceTypesRI.png)

Note the instance type(s) and number of instances running in your system. You can purchase RIs for any or all of the Datomic system instances.

## Purchasing Reserved Instances

- Browse to the ['Reserved Instances' section of the EC2 Console](https://console.aws.amazon.com/ec2/v2/home#ReservedInstances:sort=reservedInstancesId) and click the "Purchase Reserved Instances" button:

[![purchaseRI](https://docs.datomic.com/images/purchaseRI.png)](https://docs.datomic.com/images/purchaseRI.png)

- In the popup dialog, enter your desired RI search parameters and click the "Search" button.
- The example below demonstrates searching for RIs for t3.small instances for a 12-month term:

[![searchRI](https://docs.datomic.com/images/searchRI.png)](https://docs.datomic.com/images/searchRI.png)

Generally, options with higher upfront costs provide greater hourly discounts.

RIs will be automatically applied to running instance(s) matching the purchased RI attributes.

## Reference

Additional options for purchasing and applying RIs are on the [Reserved Instances guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html).

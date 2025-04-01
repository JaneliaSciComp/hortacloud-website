---
title: "Estimated Costs"
linkTitle: "Estimated Costs"
weight: 10
description: >
  Cost estimates for running the system in the cloud using AWS services
---

Deploying this system on Amazon Web Services (AWS) incurs a monthly cost for AWS service usage.

In particular, the cost breakdown is roughly:

* $382/mo - Per user costs for running the Horta client on AppStream
* $277/mo - Back-end services running on EC2 (with Savings Plan)
* $76/mo - Storage (3 TB) for one sample image on S3
* $34/mo - Virtual Private Cloud (VPC)

Therefore, the minimum total cost per month for a single user would be about $770 ($9,237/year). For 2 users, the monthly cost would be $1130 ($13,570/year), etc.  The full estimate can be [found here](https://calculator.aws/#/estimate?id=ecbba8c6713281bc419c70de414d8a1db19dd345).

We are assuming 8 hours of tracing per day, 5 days a week. If the system is not used for 8 hours a day, the cost will be less. You can dial in your own expected usage in the [AWS calculator](https://calculator.aws/#/estimate?id=ecbba8c6713281bc419c70de414d8a1db19dd345) for an accurate cost estimate.

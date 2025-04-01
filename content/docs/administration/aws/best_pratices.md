---
title: "Best Practices"
linkTitle: "Best Practices"
weight: 11
description: >
  Best practices for deployment of HortaCloud on AWS
---

The system has been designed to be easy to install and administer via standardized AWS CDK scripts and procedures which are well-documented in this online manual. In addition to following the deployment procedures, we recommend following DevOps best practices when deploying HortaCloud. These deployment practices are not enforced by the HortaCloud deployment procedures, and need to be customized for each deployment situation. 

## Deployment Best Practices 

There are many deployment best practices which cannot be exhaustively covered here, but as an example [blue-green deployments and canary deployments](https://sre.google/workbook/canarying-releases/) can be easily implemented in the context of HortaCloud:

* **Blue-green deployment**: 2 identical production environments where “blue” is the current production version and “green” is the new version. After a new release is validated, the labels are switched.
* **Canary deployment**: release new version to a subset of users. With a blue-green deployment, this can be done by asking a small subset of users to try the “green” environment before switching the labels.

In the case of severe issues, roll back to the previous version by switching labels again. In some case, the database will need to be restored to the backup made before the upgrade.

## Monitoring

To avoid unexpected system downtime, the system should be monitored so that mitigations can be applied before a system failure. This monitoring can be done manually or automated via AWS services such as CloudWatch. In particular:

* The disk space on the EC2 instance should be monitored so that the system does not run out of space. 
* Memory and CPU usage should be monitored on the EC2 instance and right-sized periodically by adjusting the EC2 instance type. 
* The size of the database should be monitored and large fragment workspaces should be periodically culled. 

## Scaling

The HortaCloud architecture was designed for flexibility and scalability. However, the default configuration is intended for supporting 10 concurrent users working with 500 million total neuron fragments across multiple workspaces.  Each workspace can support a maximum of 1 million neuron fragments at 5um spacing (taking up less than 10GB in the Database). Scaling the system past those limits will require either multiple deployments or thoughtful adaptations to the deployment. For example:

* The MongoDB database runs on the same EC2 instance as all of the other services by default. To scale the system to support more neuron fragments we recommend disaggregating the database onto a separate cluster, for example by using the [MongoDB Atlas](https://www.mongodb.com/products/platform/atlas-database) product.  
* The default system runs on a single EC2 instance which runs a single JADE service instance for serving image data. To scale to larger numbers of concurrent users, we recommend adding additional JADE services, likely by scaling on to multiple EC2 instances. This is supported by the Docker Swarm architecture but would require changes to the [AWS CDK scripts](https://github.com/JaneliaSciComp/hortacloud). 

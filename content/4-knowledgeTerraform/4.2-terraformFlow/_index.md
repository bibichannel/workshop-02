---
title : "Terrafrom flow"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

In the previous section, we briefly explored the components of Terraform. Now, let's delve into more detail about the Terraform source code provided in this workshop.

I've organized the creation of resources in Terraform into modules for easy management. There are a total of 11 modules:

- **VPC**: Contains basic resources to create a VPC in the cloud, such as subnets, route tables, security groups, etc.

- **ALB, Cloudwatch, CodeBuild, Codepipeline, ECR, ECS, Endpoint. EventBridge, S3, SNS**: Just by reading the names, you can probably guess which services they provide.

Each module will have the following files: **main.tf**, **variables.tf**, **output.tf**, and **role.tf** (contains resources to create permissions for services, depending on whether the resource requires it or not). Regarding the purpose of each file, I've outlined it in [section 4.1](../../4-knowledgeTerraform/4.1-overview/_index.vi.md).

**So how do you find the necessary resources, arguments, and attributes for each block?** You can find them here: [AWS Provider Registry Docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs). This is the latest documentation available at the time I wrote this workshop. It contains all the information you need to write Terraform code and create the services you want.

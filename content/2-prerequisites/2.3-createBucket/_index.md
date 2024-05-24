---
title : "Create S3 bucket"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

We will create a bucket to store the state file for Terraform. I'll provide a detailed explanation in [section 4](../../4-knowledgeTerraform/4.1-overview/_index.md).

1. In the Create bucket interface:
- **AWS region**: Choose the S3 bucket region based on the region where we are conducting the lab. Currently, I'm conducting the lab in the **N. Virginia (us-east-1)** region.
- **Bucket Type**: For the scope of this lab, we'll select **General purpose**.
- Enter a Bucket name, ensuring it's unique. You can choose any name; here, I'll create a bucket named `backend-tf-bibichannel`.

{{%notice info%}}
AWS S3 can be accessed publicly, and it provides us with access to its buckets and objects via the REST API.
In essence, the path must adhere to DNS and cannot have two identical domain names. Hence, bucket names need to be unique to access groups and objects through the REST API endpoint.
{{%/notice%}}

![S3](/images/2-prerequisites/2.3-createBucket/001-createBucket.png)

Below, we'll keep the default configurations and click **Create bucket**.
---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

![IMAGE](/images/1-introduce/001-introduce.png)

Looking at the architecture, you can see that this is a fairly large project involving many AWS services:
- Deploy the application across 2 AZs to increase HA and load balance to applications via Application Load Balancer (ALB).
- Create a pipeline from CodePipeline for the `streamlit` app, deploying it to the serverless Elastic Container Service.
- Use CodeBuild to build images and store them in the Elastic Container Registry (ECR).
- Receive pipeline status notifications via email with EventBridge and Amazon SNS.
- Use Route53 for a custom domain.

What if you had to manually set up these services? Or when you hand over this project to a team member, will they set it up correctly and precisely as you documented? Or when your boss sees that your project is very good and wants to apply this architecture to future projects, setting up such a large number of services makes it inevitable to make mistakes.
That is why infrastructure as code was born. It helps us to quickly deploy and minimize human errors,...

This is also my purpose in writing this article. Through this workshop, I hope to help you confidently set up a project with Terraform on the AWS Cloud platform.

Here I will have 2 repositories on GitHub for the following purposes:
1. The first repository will store the Terraform code to provision to AWS via **GitHub Actions**. This will help me to downgrade versions whenever configuration changes cause errors through version management on GitHub. And it also allows us to work together as a team.
2. The second repository will store the source code of the streamlit app. Whenever code is pushed to the repo, it will trigger a push event through a webhook for CodePipeline to be triggered, built, and deployed to ECS Fargate.

---
title : "Create CodeStar Connection"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

Let's pay attention to the architecture again. We notice there's a GitHub repository that pushes events of changes to GitHub to trigger CodePipeline.

Therefore, we also need a tool to connect GitHub to AWS. AWS acknowledges this and provides us with a tool called **CodeStar Connection**.

- Access [CodePipeline](https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1).
- Choose **Connections**.
- Select **Create connection**.

![image](/images/2-prerequisites/2.4-createCodeStar/001-createCodeStar.png)

- In the **Select a provider** section, check **GitHub**.
- Set **Connection name** to `workshop-02`.
- Choose **Connect to GitHub**.

![image](/images/2-prerequisites/2.4-createCodeStar/002-createCodeStar.png)

- Under **GitHub Apps**, click **Install a new app**.

![image](/images/2-prerequisites/2.4-createCodeStar/003-createCodeStar.png)

- You'll be redirected to the login page of your GitHub account. Log in and authorize AWS.

![image](/images/2-prerequisites/2.4-createCodeStar/004-createCodeStar.png)

- Once created, save the **ARN** of our connection to use during the Terraform code writing process.

![image](/images/2-prerequisites/2.4-createCodeStar/005-createCodeStar.png)

{{%notice note%}}
**Hold on a minute!!!** This task employs Terraform to provision infrastructure on AWS, so why don't we write code to create these services?
Initially, I had this thought too, but when we write code to create the **CodeStar Connection**, upon creation, the service will be in a **PENDING** state, and we'll need to manually intervene in the AWS Console to successfully connect it.
This would disrupt automation, making the automation process discontinuous. So, let's create this service first to ensure the IaC process runs smoothly.
{{%/notice%}}


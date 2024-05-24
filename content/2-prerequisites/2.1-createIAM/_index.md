---
title : "Create User Groups and IAM User"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

Before creating an **IAM User**, we will create a **User Group** with the necessary policies. Creating user groups helps us easily control users centrally, classify users according to their roles in the organization, and flexibly grant and revoke permissions.

Next, we assign the IAM User to the User Group we just created. This new user will inherit the permissions from the User Group.

We will use this user to authorize Terraform locally, allowing us to use Terraform to test configurations on AWS.

Access the [IAM console](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/home):
- Select **User groups**.
- Click on **Create group**.

![image](/images/2-prerequisites/2.1-createIAM/001-createIAM.png)
![image](/images/2-prerequisites/2.1-createIAM/001.1-createIAM.png)

- We will create a group named `DevOpsGroup`.

![image](/images/2-prerequisites/2.1-createIAM/002-createIAM.png)

- In the **Attach permissions policies** section, select the `AdministratorAccess` policy.

![image](/images/2-prerequisites/2.1-createIAM/003-createIAM.png)

- Click **Create user group**.

![image](/images/2-prerequisites/2.1-createIAM/004-createIAM.png)

{{% notice note%}}
**Regarding selecting the AdministratorAccess policy**. Since the goal of this workshop is not security, I will use this permission for convenience. I do not encourage you to use this wide-ranging privilege. Create a policy with the necessary limited permissions for better security.

{{% /notice %}}

- Next, we will create an **IAM User**.
- Select **Users** and click on **Create user**.
- Enter the user name as `terraform`.
- Select **Next**.

![image](/images/2-prerequisites/2.1-createIAM/005-createIAM.png)
![image](/images/2-prerequisites/2.1-createIAM/005.1-createIAM.png)

- In the **Set permissions** section, choose **Add user to group**.
- Select the `DevOpsGroup` we just created.
- Choose **Next** and then **Create User**.

![image](/images/2-prerequisites/2.1-createIAM/006-createIAM.png)

- Interface after creating the user.

![image](/images/2-prerequisites/2.1-createIAM/007-createIAM.png)

- Next, we will create an **Access key** for the user to use with AWS CLI.
- Click on the `terraform` user we just created, select the **Security Credentials** tab.

![image](/images/2-prerequisites/2.1-createIAM/008-createIAM.png)

- Scroll down to the **Access keys** section and click **Create access key**.

![image](/images/2-prerequisites/2.1-createIAM/009-createIAM.png)

- For **Use case**, select **Command Line Interface (CLI)**.

![image](/images/2-prerequisites/2.1-createIAM/010-createIAM.png)

- Check **I understand the above recommendation and want to proceed to create an access key** and select **Next**.

![image](/images/2-prerequisites/2.1-createIAM/011-createIAM.png)

- In the **Set description tag** section, enter `Check the terraform code call aws api`.
- Click **Create access key**.

![image](/images/2-prerequisites/2.1-createIAM/012-createIAM.png)

- Select **Download access key .csv**.

![image](/images/2-prerequisites/2.1-createIAM/013-createIAM.png)

- You have now completed creating the **User Group** and **IAM User**. Let's move on to the next step.
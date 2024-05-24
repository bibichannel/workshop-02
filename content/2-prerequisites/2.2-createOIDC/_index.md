---
title : "Create OIDC"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

In our architecture, we utilize a GitHub repository to perform infrastructure provisioning on AWS Cloud services. To grant GitHub the necessary permissions for this, we will need to have credentials for GitHub.

The simplest way, like using local, is to utilize the access key of our user account, provided to GitHub during the execution of Git actions. However, this requires creating a secret in GitHub, passing our AWS user credentials to a third party. During this process, if there are policies that need to be reset every 90 days, it's time-consuming to update the secret key in GitHub.

Therefore, we will employ a newer method to grant permissions. It will help us generate temporary credentials each time Git actions are run, enhancing security and manageability. That method is creating an OpenID Connect (OIDC) identity provider in IAM.

Creating an OIDC in IAM is straightforward:
- In the IAM interface, select **Identity providers**.
- Choose **Add provider**.

![image](/images/2-prerequisites/2.2-createOIDC/001-createOIDC.png)

- In the **Configure provider** section, select **OpenID Connect**.
- Enter the URL of the IdP in the **Provider URL**: `https://token.actions.githubusercontent.com`.
- In the **Audience** field, input `sts.amazonaws.com`.
- Then, select **Add provider**.

![image](/images/2-prerequisites/2.2-createOIDC/002-createOIDC.png)

- Here's the information about the **Identity providers** we just created. Remember to save the Amazon Resource Name (ARN), as we'll need it for role creation.

![image](/images/2-prerequisites/2.2-createOIDC/003-createOIDC.png)

You can find more information at: [Create an OpenID Connect (OIDC) identity provider in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)

After creating the IAM OIDC identity provider, you need to create an IAM role. This role allows GitHub's IdP to request temporary security credentials to access AWS.

- In the IAM interface, select **Roles**, then choose **Create role**.

![image](/images/2-prerequisites/2.2-createOIDC/004-createOIDC.png)

- At **Select trusted entity**, check **Custom trust policy**, then paste the following content into the editor.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": providerArn
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                },
                "StringLike": {
                    "token.actions.githubusercontent.com:sub": "repo:GitHubOrg/GitHubRepo:*"
                }
            }
        }
    ]
}
```

- For the `Principal` element, use the Amazon Resource Name (ARN) of the OIDC IdP created above.
- For the **Condition** element, use **StringEquals** to restrict permissions.
- For the StringLike element, use `repo:GitHubOrg/GitHubRepo:*`, for example, `repo:bibichannel/terraform-project:*`. This will limit IAM's ability to assume a role for GitHub in the repository you specify, which is the best practice to limit entities that can assume roles associated with IAM IdP.

You can find more information at: [Configuring a role for GitHub OIDC identity provider](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp_oidc.html#idp_oidc_Create_GitHub).

Under **Permissions policies**, add the `AdministratorAccess` permission. This is for quick setup, but in reality, apply the principle of least privilege to increase the security of your account.

![image](/images/2-prerequisites/2.2-createOIDC/005-createOIDC.png)

- Name the role: `github-oidc-role`.
- Then, select **Create role**.

![image](/images/2-prerequisites/2.2-createOIDC/006-createOIDC.png)

{{% notice note%}}
With the IAM Role we've created, it will grant the GitHub repository access to AWS resources in our account. We'll use this role during the GitHub action setup. Let's move on to the next step.
{{% /notice %}}
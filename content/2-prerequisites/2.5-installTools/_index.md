---
title : "Install tools"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.5 </b> "
---

In this section, we'll need to install **AWS CLI** and **Terraform CLI** on your local machine.

1. For AWS CLI, please refer to the following link: [Install to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

- After you have installed AWS CLI, let's create a profile to use. We'll use the **terraform** user we created in the previous step [](../../2-prerequisites/2.1-createIAM/_index.vi.md).

- Access **PowerShell** and type the following command.
```bash
aws configure --profile <profile-name>
```
- You can set any **Profile name** you want. I'll set it as **terraform**.
- Enter the necessary information.

![image](/images/2-prerequisites/2.5-installTools/001-installTools.png)

- To use the profile we just imported, set the environment variable for it:
```bash
#### CMD Windows
set AWS_PROFILE=profile_name

#### PowerShell Windows
$env:AWS_PROFILE='profile_name'

#### Linux or Mac
export AWS_PROFILE=profile_name
```
- Check again to see if the profile is being used:

```bash
aws sts get-caller-identity
```

- The result after running the above commands.

![image](/images/2-prerequisites/2.5-installTools/002-installTools.png)

2. Install Terraform

- Follow the instructions below to install Terraform on Windows: [Install Terraform for Windows](https://learn.microsoft.com/en-us/azure/developer/terraform/get-started-windows-bash?tabs=bash#5-install-terraform-for-windows)

![image](/images/2-prerequisites/2.5-installTools/003-installTools.png)

- After installation, check the version you are using. Currently, my version is `v1.7.3`.
{{% notice note%}}
Since Terraform is open-source, it is supported and updated by the community very quickly. So when you perform this workshop, your version may be outdated, and some features in the provided Terraform code may be outdated. But don't worry, because the documentation is constantly updated according to the version, so you can refer to it and make modifications as needed.

{{% /notice %}}
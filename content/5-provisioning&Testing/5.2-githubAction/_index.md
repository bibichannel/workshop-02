---
title : "Git action"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---

With provisioning infrastructure on GitHub Actions, it's easier, and you don't need to prepare anything.

- Access the Terraform repository you created in [step 3.1](../../3-setupGithub/3.1-createTerraformRepo/_index.vi.md).
- Select **Actions**, click on **Deploy Infrastructure**, then choose **Run workflow**.
- Choose the necessary parameters for your needs and click **Run workflow**.

![image](/images/5-provisioning&Testing/5.2-githubAction/001-githubAction.png)

You can access to view details, here I'm only running **terraform plan**.

![image](/images/5-provisioning&Testing/5.2-githubAction/002-githubAction.png)

Select **apply** for **Selecting actions for terraform** and rerun, check the result as in [step 5.1](../../5-provisioning&Testing/5.1-local/_index.md).

{{%notice info%}}
Here, I'm running a test run. But in reality, it will automatically run whenever the Terraform code is edited and pushed. You can try adding trigger conditions in the *.github/workflows/terraform-deploy.yml* file.

{{%/notice%}}
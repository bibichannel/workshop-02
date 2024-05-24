---
title : "Prepare github repository"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

In this workshop, we'll need 2 GitHub repositories:

1. The first repository will store Terraform code for provisioning resources to AWS through **Git actions**. This will allow us to downgrade versions whenever configuration changes cause issues through version management on GitHub. Additionally, it enables us to collaborate effectively.
2. The second repository will store the source code of the Streamlit app. Whenever code is pushed to this repository, it will trigger a push event through a webhook to trigger CodePipeline, which will build and deploy it to ECS Fargate.

### Contents
 - [Terraform IaC to AWS Cloud](3.1-createTerraformRepo/)
 - [Streamlit application](3.2-createStreamlitRepo/)
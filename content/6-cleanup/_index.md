---
title : "Resource Cleanup"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

Sure, here are the steps to sequentially delete the resources:

1. If you are using Terraform locally, use the following command to destroy the resources:

```bash
terraform destroy -auto-approve
```

2. Run the workflow with the parameter **destroy** in **Selecting actions for terraform** to delete the provisioned infrastructure.

3. Delete the repository if necessary.
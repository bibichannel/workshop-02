---
title : "Local"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

### Preparation
When running locally, we need to have two additional files: **terraform.tfvars** and **vars.tfbackend**. These files will assign values to the environment variables defined in the variables.tf file in the root directory and configure the backend to store state in an S3 bucket.

1. For **terraform.tfvars**, you will have the following content:
```tfvars
aws_region              = "us-east-1"
stage_name              = "staging"
project_name            = "workshop-02"
create_by               = <Your name>
github_repo             = <GitHubOrg/GitHubRepo>
github_branch           = "main"
codestar_connection_arn = <CodeStar Conneciton ARN>
account_id              = <Account ID AWS>
```
- The **github_repo** variable is the name of the GitHub repo containing the source code for the Streamlit app created in [step 3.2](../../3-setupGithub/3.2-createStreamlitRepo/_index.md). This variable is used to configure the **stage source** of **Codepipeline services**.
- The **github_branch** variable is the branch of the GitHub repo containing the source code of the Streamlit app you want to monitor. This variable is used to configure the **stage source** of **Codepipeline services**.
- The **codestar_connection_arn** variable contains the ARN of the Connection created in [step 2.4](../../2-prerequisites/2.4-createCodeStar/_index.md). This variable is used to configure the **stage source** of **Codepipeline services**.
- The **account_id** variable contains your AWS account ID, which is used to create the role for the **Amazon Simple Notification Service (SNS) service**.
- In addition to the listed variables, I've provided placeholders for other common variables used across modules or for tagging resources.

2. For **vars.tfbackend**, you will have the following content:
```tfbackend
bucket = <Bucket's name>
key    = "terraform-staging/terraform.tfstate"
region = "us-east-1"
```
- Configuring the backend for Terraform to store the state file in an S3 bucket requires declaring variables as above. Using a backend helps in collaborating on Terraform code, securing the state file, etc.
- The **bucket** variable contains the name of the bucket you use to store the backend as created in [step 2.3](../../2-prerequisites/2.3-createBucket/_index.md).
- The **key** variable configures the prefix for the path to the Terraform file in the bucket.
- The **region** variable determines the region where your bucket is located.
- You can read more about setting up a backend for Terraform at [backend s3](https://developer.hashicorp.com/terraform/language/settings/backends/s3).

3. Open the **main.tf** file in the root directory of the source code and navigate to the **locals block**:
```tf
locals {
  tags = {
    Project     = var.project_name
    CreateBy    = var.create_by
    Environment = var.stage_name
  }
  container_name     = "streamlit-app"
  container_port     = 8501
  container_cpu      = 512
  container_memory   = 1024
  notification_email = "trung.lykhanh150901@gmail.com"
}
```

- In addition to the tags list containing key:value pairs with values passed from the **terraform.tfvars** file, we are interested in the following variables:
- **container_name** is the variable used to name the container in the ECS services configuration.
- **container_port** is the port exposed by the container.
- **container_cpu** sets the CPU limit for Fargate task definitions.
- **notification_email** is the email registered with the SNS service to receive notifications of state changes from CodePipeline.

Adjust the values of these variables to suit your needs.

### Execution
After you have these two files, run the following commands one by one:
```bash
terraform init -backend-config='vars.tfbackend'
```

- This command will install plugins for the providers you have declared in the **providers.tf** file, initialize a Terraform environment in the current directory, and configure the backend using the parameters from the **vars.tfbackend** file through the **-backend-config** flag. It also loads the modules we've written.

- The following image shows the output after running this command:

![image](/images/5-provisioning&Testing/5.1-local/001-local.png)

```bash
terraform validate
```

- This command is used to check if all your Terraform configuration files have correct syntax and are valid without making any actual changes to the infrastructure. It helps you catch syntax errors or logic errors before applying any changes.

- The following image shows the output after running this command:

![image](/images/5-provisioning&Testing/5.1-local/002-local.png)

```bash
terraform plan
```

- This command generates a plan to perform changes on the infrastructure based on the Terraform configuration and the current state without actually making any changes. It allows you to preview what will happen if you apply these changes.

- When you run **terraform plan**, Terraform reads your configuration and the current state, then compares them to determine what changes need to be made to achieve the desired state. The output of this command will display a list of actions Terraform will take, including creating, updating, or deleting resources.

- It also provides information about which resources will be changed, added, and removed (if any). Additionally, it provides an estimate of the cost and time required to implement the changes.

- The following image shows the output after running this command:

![image](/images/5-provisioning&Testing/5.1-local/003-local.png)

```bash
terraform apply -auto-approve
```

- The **terraform apply -auto-approve** command applies the changes planned by Terraform without requiring additional confirmation from the user. This means Terraform will automatically apply the changes without needing any further confirmation.

- The following image shows the output after running this command:

![image](/images/5-provisioning&Testing/5.1-local/004-local.png)

- Some resources may take time to create, but once they are created, you will receive an **apply complete** message.

### Check on AWS Console
Access the following consoles to check the information of the resources you have created:

1. VPC console [link](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#vpcs:)
2. Codepipeline console [link](https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1)
3. ECR console [link](https://us-east-1.console.aws.amazon.com/ecr/private-registry/repositories?region=us-east-1)
4. ECS console [link](https://us-east-1.console.aws.amazon.com/ecs/v2/clusters?region=us-east-1)
5. SNS topic console [link](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/topics)
6. EvenBridge rule console [link](https://us-east-1.console.aws.amazon.com/events/home?region=us-east-1#/rules)
7. Endpoint console [link](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Endpoints:)
8. S3 bucket [link](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1)
9. ALB console [link](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#LoadBalancers:)

Information about codepipeline state changes is sent to the registered email. Remember to check your email early for your SNS services **Subscription** email.

![image](/images/5-provisioning&Testing/5.1-local/005-local.png)

Access the following information to see if the website has been built.

![image](/images/5-provisioning&Testing/5.1-local/006-local.png)

| Container 1 | Container 2 |
|:-----------:|:-----------:|
| ![image](/images/5-provisioning&Testing/5.1-local/007-local.png) | ![image](/images/5-provisioning&Testing/5.1-local/008-local.png) |

As we can see, the website is functioning properly, and the load balancer routes traffic to 2 containers.

{{%notice info%}}
Have you noticed that here I use the **DNS** of the **load balancer**? But in the initial architecture, we also configured **route53**! Because following a workshop straight through won't awaken your curiosity, so in the final module, I'll let you write it yourself and deploy a domain name that you register. Challenge yourself and create a complete system for this picture. I believe you can do it if you've read this far.

{{%/notice%}}

#### Push new code to the streamlit app repository
Change any code or text in the directory containing the source code of the streamlit app and push it to GitHub.
Return to the [Codepipeline console](https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1) to see if the pipeline is functioning correctly. Check the email response when the codepipeline changes state and check ECS Fargate services to see if tasks have been created or not.

### Destroy infrastructure
Run the following command to destroy the infrastructure you provisioned.

```bash
terraform destroy -auto-approve
```

![image](/images/5-provisioning&Testing/5.1-local/009-local.png)
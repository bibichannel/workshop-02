---
title : "Overview"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

### 1. Overview of Terraform
Terraform is an open-source tool by HashiCorp, specialized in provisioning infrastructure. With Terraform, we simply write code and execute a few simple CLI commands, and it will create infrastructure for us.

The language Terraform uses is called HashiCorp Configuration Language (HCL).

The flow of Terraform is as follows: we write code, then execute CLI commands, and it will provision infrastructure. Once it's done, it generates a state file to store the current infrastructure architecture. The state is a record of the current infrastructure that Terraform manages. It's used to track resources created, modified, or deleted. The state also helps Terraform determine the changes needed to achieve the desired state of the infrastructure.

- State file: By default, Terraform stores state in a file named **terraform.tfstate** in the current working directory. This file contains information about all resources Terraform manages.

- State Backend: Terraform can store state on remote storage services like Amazon S3, Google Cloud Storage, Azure Blob Storage, or Terraform Cloud. This helps share state among team members and keeps it secure.

### 2. How Terraform Connects to Clouds

1. Terraform sends requests to the public APIs of clouds.
2. On the cloud side, it authenticates these requests. Terraform uses providers to attach information during the authentication process. Providers can be Google Cloud Platform provider, AWS provider, Azure provider, etc.
3. To support the authentication process, we need service accounts (IAM) to provide permissions.

### 3. Terraform Lifecycle

![IMAGE](/images/4-knowledgeTerraform/4.1-overview/001-overview.png)

When working with Terraform, it's essential to understand these four basic commands:

- **Terraform Init**: Used to initialize the local environment. This command reads the scripts we've created, checks the version, and downloads necessary API packages for deployment. Run terraform init the **first time** or when adding some **providers** or **public/private modules** into our **terraform code**.

- **Terraform Plan**: Compares the state of the **local** environment and the one on **cloud** (specifically comparing within the state file). It provides an execution plan, such as changes before or after deployment. This process does not deploy anything to the cloud.

- **Terraform Apply**: Executes the deployment, pushing changes of the source code to the cloud. It creates state files.

- **Terraform Destroy**: As the name suggests, it **deletes** all the **previously deployed infrastructure**.

### 4. Blocks in Terraform

![IMAGE](/images/4-knowledgeTerraform/4.1-overview/002-overview.png)

#### 1. Provider Block
The **Provider block** is used to define and configure the service provider Terraform will interact with, such as AWS, Azure, Google Cloud,...

#### 2. Terraform Block
The **Terraform block** is used to configure global settings of Terraform, such as required version of Terraform, backend, and default providers.

#### 3. Resource Block
The **Resource block** is used to create and manage resources within the service provider, such as EC2 instances, S3 buckets,...

#### 4. Variable Block
The **Variable block** is used to define input variables, allowing users to customize values in Terraform configuration.

#### 5. Module Block
The **Module block** is used to call and use Terraform modules, facilitating configuration reuse and better source code organization.

#### 6. Data Block
The **Data block** is used to query information about existing resources or services from the service provider without creating or changing any resources.

#### 7. Locals Block
The **Locals block** is used to define local variables, simplifying and reusing complex values in Terraform configuration.

#### 8. Output Block
The **Output block** is used to define output variables, providing useful information after infrastructure deployment. These outputs may include resource IDs, IP addresses, etc.

#### 9. Backend Block
The **Backend block** is used to define and configure the backend, where Terraform state will be stored (such as in S3, Azure Blob Storage, etc.).

{{%notice info%}}
These blocks are written in **.tf** files, below are common **.tf** files for organizing Terraform code effectively.

{{%/notice%}}

### 5. Files in Terraform

#### 1. main.tf
**main.tf** is the main file in Terraform configuration, containing resource definitions (resource blocks) and necessary configurations for infrastructure deployment (locals block, terraform block, module block,...).

#### 2. variables.tf
**variables.tf** is used to define input variables (variables block) that can be referenced in other configuration files. This helps in reusability and easy management of configurations.

#### 3. output.tf
**output.tf** is used to define output variables (output block), providing useful information after infrastructure deployment. These outputs may include resource IDs, IP addresses,...

#### 4. vars.tfbackend
**vars.tfbackend** is commonly used to configure the backend for Terraform, where the Terraform state will be stored (such as in S3, Azure Blob Storage, etc.). This file is often included in the terraform init command to set up the backend.

#### 5. terraform.tfvars
**terraform.tfvars** is used to provide default values for input variables defined in variables.tf. This file helps separate configuration and variable values, allowing easy configuration changes without editing the main configuration file.

#### 6. providers.tf
**providers.tf** is used to define service providers (providers block) Terraform will interact with, such as AWS, Azure, Google Cloud, etc. This file may also contain configurations for providers, such as region, credentials, etc.

{{%notice info%}}
Separating these configurations helps maintain and manage Terraform projects easily, ensuring clear and organized configurations.
{{%/notice%}}
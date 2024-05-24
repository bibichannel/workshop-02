---
title : "Terraform IaC to AWS Cloud"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

#### The source code for this part is available here:

 - [terraform-aws-continuous-docker-deployment-to-aws-fargate](https://github.com/bibichannel/terraform-aws-continuous-docker-deployment-to-aws-fargate).

#### You can fork it directly from my repository:
- With the **Repository name** matching the one you set when creating the role for OIDC in [step 2.2](../../2-prerequisites/2.2-createOIDC/_index.md).

- Click **Create fork**.

- In the repository interface you just created or forked, go to **settings**.

- Select **Secrets and variables**, then click **Actions**.

- We will proceed to create **2 new repository secrets** with the following content:

  - `TERRAFORM_BUCKET_NAME`: the name of the **bucket** you created in [step 2.3](../../2-prerequisites/2.3-createBucket/_index.vi.md).
  - `CODESTAR_CONNECTION_ARN`: containing the ARN of the **connection** we created in [step 2.4](../../2-prerequisites/2.4-createCodeStar/_index.md).

![image](/images/3-setupGithub/3.1-createTerraformRepo/002-createTerraformRepo.png)

With these 2 secret variables, they will be used during the action execution when the GitHub workflow is activated.

#### Let's briefly explore the components in this repository
The directory structure of this repo is as follows:

![image](/images/3-setupGithub/3.1-createTerraformRepo/003-createTerraformRepo.png)

Here we have 3 main directories:
1. **modules**: contains Terraform modules.
2. **.github**: contains GitHub workflow files, used to run GitHub actions.
3. **static**: contains image directories for writing Readme.md files.

In addition to files with the **.tf** extension, which I'll introduce later, we'll focus on the following files:
1. **terraform-deploy.yml**: is a YAML configuration file used in the pipeline of (CI/CD) to automatically deploy infrastructure using Terraform. Below is the content of the file.

```yml
name: Deploy Infrastructure

on:
  workflow_dispatch:
    inputs:
      actions:
        description: "Selecting actions for terraform"
        required: true
        default: "plan"
        type: choice
        options:
          - plan
          - apply
          - destroy
      environment:
        description: "Selecting evironments for terraform"
        required: true
        default: "staging"
        type: choice
        options:
          - staging
          - prod
env:
  AWS_REGION : "us-east-1" 

permissions:
  id-token: write # This is required for requesting the JWT
  contents: read  # This is required for actions/checkout
          
jobs:
  build:
    runs-on: ubuntu-latest
    name: DEPLOY INFRASTRUCTURE
    environment: ${{ github.event.inputs.environment }}
    steps:
      - name: Check out repository code
        uses: actions/checkout@v3

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-region: ${{ env.AWS_REGION }}
          role-to-assume: arn:aws:iam::590183956208:role/github-oidc-role
          role-session-name: rolesession
      
      - name: Get account id
        run: |
          ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
          echo "AWS_ACCOUNT_ID=$ACCOUNT_ID" >> $GITHUB_ENV

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: 1.7.3
          terraform_wrapper: false

      - name: Prepare Terraform
        env:
          PROJECT_NAME: "workshop-02"
          STAGE_NAME: ${{ github.event.inputs.environment }}
          TERRAFORM_BUCKET_NAME: ${{ secrets.TERRAFORM_BUCKET_NAME }}
          CODESTAR_CONNECTION_ARN: ${{  secrets.CODESTAR_CONNECTION_ARN }}
          YOUR_GITHUB_REPOSITORY: "GitHubOrg/GitHubRepo"
          YOUR_GITHUB_BRANCH: "main"
        run: |
          chmod +x setup.sh
          bash setup.sh
          terraform init -backend-config=./vars.tfbackend

      - name: Plan Terraform
        run: |
          terraform plan
        if: ${{ github.event.inputs.actions == 'plan' }}

      - name: Aplly Terraform
        run: |
          terraform apply -auto-approve
        if: ${{ github.event.inputs.actions == 'apply' }}

      - name: Destroy Terraform
        run: |
          terraform destroy -auto-approve
        if: ${{ github.event.inputs.actions == 'destroy' }}
```

- With **workflow_dispatch**, it allows manual activation with inputs:
  - **actions**: Choose Terraform actions (plan, apply, destroy).
  - **environment**: Choose environment (staging, prod).
  
- Setting up permissions for using OIDC, used in the step named **Configure AWS Credentials**:
  - **id-token: write** is the necessary permission to request JWT. With JWT to get temporary AWS authentication information.
  - You can read more here: [configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials?tab=readme-ov-file#oidc)

- Here, we pay attention to the important step named **Prepare Terraform**, in this step, we need to define the environment variables for the step, get and use variables from GitHub secrets.
  - Besides the variables that you can understand by reading their names, there will be 2 variables representing the repository containing the source code for your application.
    - **YOUR_GITHUB_REPOSITORY: "GitHubOrg/GitHubRepo"**
    - **YOUR_GITHUB_BRANCH: "main"**
  - Then we will run the **setup.sh** script and initialize Terraform with backend configuration. This part might be confusing for those who are not familiar with Terraform, but don't worry, I'll explain it later.

2. About the **setup.sh** file.
- After being executed, this file will create 2 files: **terraform.tfvars** and **vars.tfbackend**, containing variables for setting up Terraform.

{{%notice info%}}
If you don't understand the contents of the workflow file, about GitHub action and its components, please read more about [GitHub action](https://docs.github.com/en/actions) to better understand.

{{%/notice%}}

#### Regarding secret variables
Let's follow this flow to understand better:

![image](/images/3-setupGithub/3.1-createTerraformRepo/004-createTerraformRepo.png)

When the workflow is triggered, GitHub action will proceed to retrieve secret variables and decrypt them to access the variable values. Because the variables are encrypted, the process is secure.

Next, the variables will be passed as follows:

![image](/images/3-setupGithub/3.1-createTerraformRepo/005-createTerraformRepo.png)

- To the **Prepare terraform** step, where we have defined the variables for this step, it will create 2 files **terraform.tfvars** and **vars.tfbackend**. These are files containing the necessary variables to set up and pass variables to Terraform modules for use. When we have provided all the input variables, Terraform will be executed and infrastructure will be provisioned on AWS Cloud.
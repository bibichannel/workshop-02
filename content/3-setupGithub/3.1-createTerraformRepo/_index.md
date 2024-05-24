---
title : "Terraform IaC đến AWS Cloud"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

#### Source code bạn dùng cho phần này sẽ ở đây
 - [terraform-aws-continuous-docker-deployment-to-aws-fargate](https://github.com/bibichannel/terraform-aws-continuous-docker-deployment-to-aws-fargate).

#### Bạn có thể fork thẳng từ repo của mình về
- Với **Repository name** là tên trùng với tên bạn đặt lúc tạo role cho OIDC [bước 2.2](../../2-prerequiste/2.2-createOIDC/_index.vi.md)

- Nhấn **Create fork**.

- Ở giao diện repository bạn vừa tạo hoặc fork ta truy cập **setting**.

- Chọn **Secrets and variables**, click vào **Actions**.

- Ta sẽ tiến hành tạo **2 repository secrets** mới với nội dung như sau:

  - `TERRAFORM_BUCKET_NAME` là tên của **bucket** bạn đã tạo ở [bước 2.3](../../2-prerequiste/2.3-createBucket/_index.vi.md).
  - `CODESTAR_CONNECTION_ARN` chứa ARN của **connection** ta tạo ở [bước 2.4](../../2-prerequiste/2.4-createCodeStar/_index.vi.md).

![image](/images/3-setupGithub/3.1-createTerraformRepo/002-createTerraformRepo.png)

Với 2 variables secrets trên sẽ được sử dụng trong quá trình chạy action khi workflow github được kích hoạt.

#### Chúng ta cùng tìm hiểu sơ qua các thành phần trong repository này
Cây thư mục của repo này như sau:

![image](/images/3-setupGithub/3.1-createTerraformRepo/003-createTerraformRepo.png)

Ở đây sẽ có 3 thư mục chính đó là:
1. **modules**: chứa các module của terraform.
2. **.github**: chứa workflow file của github, dùng để chạy github action.
3. **static**: chứa thư mục hình ảnh cho viết Readme.md file.

Ngoài các file có đuôi **.tf** mình sẽ giới thiệu về phần sau, thì ta sẽ quan tâm những file sau:
1. **terraform-deploy.yml**: là một file cấu hình YAML dùng trong pipeline của (CI/CD) để tự động triển khai cơ sở hạ tầng bằng Terraform. Dưới đây sẽ là nội dung của file.
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
- Với **workflow_dispatch** sẽ cho phép kích hoạt thủ công với các input:
  - **actions**: Lựa chọn hành động Terraform (plan, apply, destroy).
  - **environment**: Lựa chọn môi trường (staging, prod).
  
- Thiết lập quyền cho việc sử dụng OIDC, được sử dụng ở step có tên là **Configure AWS Credentials**:
  - **id-token: write** là quyền cần thiết để yêu cầu JWT. Với JWT để lấy thông tin xác thực AWS tạm thời.
  - Bạn có thể đọc thêm ở đây: [configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials?tab=readme-ov-file#oidc)

- Ở đây ta sẽ chú ý tới step quan trọng có tên là **Prepare Terraform**, ở step này ta cần phải định nghĩa các biến môi trường cho step, lấy và sử dụng các variables từ secret github.
  - Ngoài các biến mà bạn đọc tên cũng biết công dụng ở đó thì đây sẽ là 2 biến đại diện cho repository chứ source code cho application của bạn.
    - **YOUR_GITHUB_REPOSITORY: "GitHubOrg/GitHubRepo"**
    - **YOUR_GITHUB_BRANCH: "main"**
  - Sau đó ta sẽ chạy script **setup.sh** và khởi tạo Terraform với cấu hình backend. Đoạn này nếu bạn nào chưa biết về terraform sẽ bắt đầu thấy rối rối đúng không. Mình sẽ giải thích rõ ở phần sau nhé.

2. Về file **setup.sh**.
- File này sau khi được chạy sẽ tạo ra 2 file là **terraform.tfvars** và  **vars.tfbackend**. Chứa các biến cho việc setup terraform.

{{%notice info%}}
Nếu bạn chưa hiểu về nội dung file workflow, về github action và các component trong đó, hãy đọc thêm [github action](https://docs.github.com/en/actions) để hiểu rõ hơn nhé. 

{{%/notice%}}

#### Về phần secret variables
Ta có flow sau để bạn có thể hiểu hơn

![image](/images/3-setupGithub/3.1-createTerraformRepo/004-createTerraformRepo.png)

Khi worklow được trigger, gihub action sẽ thực hiện lấy secret variables và decrypt nó ra để có thể truy cập được value của biến. Vì các biến được mã hoá nên quá trình là bảo mật.

Tiếp theo các biến sẽ được pass như sau:

![image](/images/3-setupGithub/3.1-createTerraformRepo/005-createTerraformRepo.png)

- Tới step **Prepare terraform**, ở đây chúng ta đã định nghĩa các biến mỗi trường cho step này nó sẽ tạo ra 2 file **terraform.tfvars** và  **vars.tfbackend**. Đây đều là 2 file chứa các biến cần thiết để setup và truyền biến vào các module terraform, để các module có thể sử dụng. Khi chúng ta đã cung cấp đủ các input variables rồi thì terraform sẽ được chạy và provisioning infrastructure lên AWS Cloud.
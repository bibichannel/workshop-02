---
title : "Local"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

### Chuẩn bị
Với việc chạy trên local thì chúng ta cần phải có thêm 2 tệp nữa là **terraform.tfvars** và  **vars.tfbackend**. Hai tệp này sẽ gán value các biến môi trường đã được định nghĩa trong tệp variables.tf ở thư mục root và cấu hình backend lưu trữ trên S3 bucket.

1. Với **terraform.tfvars** bạn sẽ có nội dung như sau:
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
- Biến **github_repo** là tên của github repo chứa source code streamlit app tạo ở [bước 3.2](../../3-setupGithub/3.2-createStreamlitRepo/_index.vi.md). Biến này được sử dụng cấu hình cho **stage source** của **Codepipeline services**.
- Biến **github_branch** là nhánh của github repo chứa source code streamlit app bạn muốn theo dõi trạng thái. Biến này được sử dụng cấu hình cho **stage source** của **Codepipeline services**.
- Biến **codestar_connection_arn** chứa ARN của Connection tạo ở [bước 2.4](../../2-prerequisites/2.4-createCodeStar/_index.vi.md). Biến này được dùng cho việc cấu hình **stage source** của **Codepipeline services**.
- Biến **account_id** này chứa id của aws account của bạn, biến này được sử dụng cho việc tạo role của dịch vụ Amazon Simple Notification Service (SNS).
- Ngoài các biến mình để cập các biến còn lại sẽ là các common variables cho các modules dùng hoặc để đánh tags các resources.
- 
2. Với **vars.tfbackend** bạn sẽ có nội dung như sau:
```tfbackend
bucket = <Bucket's name>
key    = "terraform-staging/terraform.tfstate"
region = "us-east-1"
```
- Với việc cấu hình **backend** cho terraform lưu trữ state file trên S3 sẽ cần khai báo những biến như trên. Việc sử dụng backend sẽ giúp ta khi code terraform có thể teamwork với nhau, bảo mật state file,...
- Biến **bucket** chứa thông tin bucket bạn dùng để lưu backend mà ta đã tạo ở [bước 2.3](../../2-prerequisites/2.3-createBucket/_index.vi.md).
- Biến **key** cấu hình prefix cho đường dẫn tới file terraform trong bucket.
- Biến **region** xác định xem bucket của bạn nằm ở region nào.
- Bạn có thể đọc thêm thông tin về cách tạo backend cho terraform tại [backend s3](https://developer.hashicorp.com/terraform/language/settings/backends/s3).

3. Bạn truy cập file **main.tf** ở root directory trong source code, tìm tới **locals block**:
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

- Ngoài tags list chứa các key:value với value được pass từ **terraform.tfvars** file thì ta sẽ quan tâm tới các biến sau.
- **container_name** là biến đặt tên cho container trong cấu hình ECS services.
- **container_port** port của containter được expose. 
- **container_cpu** giới hạn cpu cho fargate task definitions.
- **notification_email** là email được đăng ký với dịch vụ SNS, nhận thông báo change state của Codepipeline.

Hãy thay đổi thông tin các biến cho phù hợp với bạn.

### Tiến hành chạy
Sau khi bạn đã có 2 tệp trên thì bây giờ bạn chạy lần lượt câu lệnh sau:
```bash
terraform init -backend-config='vars.tfbackend'
```

- Câu lệnh này sẽ tiến hành cài đặt các plugin cho provider bạn đã khai báo trong **providers.tf** file, khởi tạo một môi trường Terraform trong thư mục hiện tại. Cấu hình backend sử dụng các thông số từ **vars.tfbackend** file, thông qua cờ -**backend-config**. Load các module ta đã viết.

- Dưới đây là hình ảnh sau khi chạy câu lệnh trên.

![image](/images/5-provisioning&Testing/5.1-local/001-local.png)

```bash
terraform validate
```

- Được sử dụng để kiểm tra xem tất cả các tệp cấu hình Terraform của bạn có đúng cú pháp và hợp lệ hay không, mà không cần thực hiện bất kỳ thay đổi nào đến hạ tầng thực tế. Điều này giúp bạn phát hiện lỗi cú pháp hoặc lỗi logic trước khi áp dụng bất kỳ thay đổi nào.

- Dưới đây là hình ảnh sau khi chạy câu lệnh trên.

![image](/images/5-provisioning&Testing/5.1-local/002-local.png)

```bash
terraform plan
```

- Được sử dụng để tạo ra một kế hoạch thực hiện các thay đổi trên hạ tầng dựa trên cấu hình Terraform và state hiện tại mà không thực hiện các thay đổi thực tế. Điều này cho phép bạn xem trước những gì sẽ xảy ra nếu bạn áp dụng các thay đổi này.

- Khi bạn chạy terraform plan, Terraform sẽ đọc cấu hình của bạn và state hiện tại, sau đó so sánh chúng để xác định những thay đổi cần thực hiện để đạt được trạng thái mới. Kết quả của lệnh này sẽ hiển thị danh sách các hành động mà Terraform sẽ thực hiện, bao gồm việc tạo, cập nhật hoặc xóa các tài nguyên.

- Nó cũng cung cấp thông tin về tài nguyên nào sẽ được thay đổi, tài nguyên nào sẽ được thêm vào, và tài nguyên nào sẽ bị xóa (nếu có). Ngoài ra, nó cung cấp một ước lượng về chi phí và thời gian cần thiết để thực hiện các thay đổi.

- Dưới đây là hình ảnh sau khi chạy câu lệnh trên.

![image](/images/5-provisioning&Testing/5.1-local/003-local.png)

```bash
terraform apply -auto-approve
```

- Lệnh terraform apply -auto-approve sẽ thực hiện áp dụng các thay đổi đã được kế hoạch bởi Terraform mà không cần xác nhận thêm từ người dùng. Điều này có nghĩa là Terraform sẽ tự động thực hiện các thay đổi mà không cần phải yêu cầu sự xác nhận nào từ bạn.

- Dưới đây là hình ảnh sau khi chạy câu lệnh trên.

![image](/images/5-provisioning&Testing/5.1-local/004-local.png)

- Sẽ có những resources mất thời gian để tạo nhưng sau khi tạo xong nó sẽ thông thông báo **apply compelete**.

### Kiểm tra trên AWS Consonle
Truy cập lần lượt các console sau để kiểm tra thông tin resources ta đã tạo:

1. VPC console [link](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#vpcs:)
2. Codepipeline console [link](https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1)
3. ECR console [link](https://us-east-1.console.aws.amazon.com/ecr/private-registry/repositories?region=us-east-1)
4. ECS console [link](https://us-east-1.console.aws.amazon.com/ecs/v2/clusters?region=us-east-1)
5. SNS topic console [link](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/topics)
6. EvenBridge rule console [link](https://us-east-1.console.aws.amazon.com/events/home?region=us-east-1#/rules)
7. Enpoint console [link](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Endpoints:)
8. S3 bucket [link](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1)
9. ALB console [link](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#LoadBalancers:)

Thông tin về state change của codepipeline được gửi về email đã đăng ký. Bạn nhớ check mail sớm để **Subscription** email của bạn cho SNS services nhé.

![image](/images/5-provisioning&Testing/5.1-local/005-local.png)

Truy cập thông tin sau dây để xem thử website đã được build.

![image](/images/5-provisioning&Testing/5.1-local/006-local.png)

| Container 1 | Container 2 |
|:-----------:|:-----------:|
| ![image](/images/5-provisioning&Testing/5.1-local/007-local.png) | ![image](/images/5-provisioning&Testing/5.1-local/008-local.png) |

Như ta thấy web đã hoạt động tốt, loadbalancer định tuyến traffic vào 2 container.

{{%notice info%}}
Bạn có để ý thấy ở đây mình dùng **DNS** của **loadbalancer**. Nhưng trong architect ban đầu có cấu hình thêm **route53** mà!!! Vì làm một mạch theo workshop luôn sẽ không đánh thức sự tò mò của bạn nên module cuối cùng mình sẽ để các bạn tự viết nó và triển khai một tên miền do chính các bạn đăng ký. Hãy thử thách bản thân và tạo nên một hệ thống hoàn chỉnh cho bức tranh này nhé. Mình tin bạn có thể làm được nếu đã đọc tới đây.

{{%/notice%}}

#### Thực hiện push code mới lên repository streamlit app
Bạn hãy thay đổi bất kì đoạn mã hay văn bản nào trong thư mục chứa source code của streamlit app và push nó lên github.
Quay lại [Codepipeline console](https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1) để xem pipeline có được hoạt động đúng không. Check email trả về khi codepipeline thay đổi trạng thái và check ECS fargate services xem task đã được tạo mới hay chưa nhé.

### Destroy cơ sở hạ tầng
Chạy câu lệnh sau để huỷ cơ sở hạ tầng bạn đã provisioned.

```bash
terraform destroy -auto-approve
```

![image](/images/5-provisioning&Testing/5.1-local/009-local.png)
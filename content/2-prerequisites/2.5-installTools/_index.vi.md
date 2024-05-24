---
title : "Cài đặt tools"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 2.5 </b> "
---

Ở phần này ta sẽ cần cài đặt **AWS CLI** và **Terraform CLI** dưới local.

1. Để cài đặt AWS CLI hãy xem link sau: [Install to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

- Sau khi bạn đã cài đặt được AWS CLI, ta thực hiện tạo profile để sử dụng. Lúc này ta sẽ sử dụng **terraform** user ta đã tạo ở bước [](../../2-prerequisites/2.1-createIAM/_index.vi.md).

- Truy cập **PowerShell** gõ câu lệnh sau.
```bash
aws configure --profile <profile-name>
```
- **Profile name** tuỳ bạn đặt, với mình sẽ đặt là **terraform**.
- Tiến hành nhập các thông tin cần thiết.

![image](/images/2-prerequisites/2.5-installTools/001-installTools.png)

- Để sử dụng **profile** ta vừa import ta sẽ set biến môi trường cho nó:
```bash
#### CMD Windows
set AWS_PROFILE=profile_name

#### Powershell Windows
$env:AWS_PROFILE='profile_name'

#### Linux or Mac
export AWS_PROFILE=profile_name
```
- kiểm tra lại xem **profile** đã được sử dụng chưa

```bash
aws sts get-caller-identity
```

- kết quả sau khi chạy các câu lệnh trên, mình đã sử dụng được đúng **profile** mình muốn.

![image](/images/2-prerequisites/2.5-installTools/002-installTools.png)

2. Thực hiện cài đặt terraform

- Thực hiện theo hướng dẫn sau đây để cài đặt terraform trên windows: [Install Terraform for Windows](https://learn.microsoft.com/en-us/azure/developer/terraform/get-started-windows-bash?tabs=bash#4-install-terraform-for-windows)

![image](/images/2-prerequisites/2.5-installTools/003-installTools.png)

- Sau khi cài đặt xong hãy check version bạn sử dụng, hiện tại version của mình là `v1.7.3`. 
{{% notice note%}}
Vì terraform là open-source nên nó được cộng đồng hỗ trợ và cập nhật rất nhanh, nên khi bạn thực hiện workshop này có thể version của mình sẽ bị lỗi thời và một vài tính năng trong code terraform mình cung cấp có thể bị outdate. Nhưng đừng lo, vì document được cập nhật liên tục theo version nên bạn có thể tham khảo và tự sửa đổi cho phù hợp.

{{% /notice %}}


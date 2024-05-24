---
title : "Terrafrom flow"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 4.2 </b> "
---

Ở phần trước ta cũng đã tìm hiểu sơ bộ về các thành phần terraform rồi, thì ở phần này mình sẽ nói chi tiết hơn về source code terraform mình đã cung cấp qua workshop này.

Mình tổ chức việc tạo các resources trong terraform theo dạng module để dễ dàng quản lý. Có tất cả là 11 module.
- **VPC**: chứa các resources cơ bản tạo nên một VPC trong cloud như: subnet, route table, security groups,...

- **ALB, Cloudwatch, CodeBuild, Codepipeline, ECR, ECS, Endpoint. EventBridge, S3, SNS**: đọc tên thôi thì bạn cũng có thể biết là cung cấp cho services gì rồi đúng không.

Trong mỗi modlue sẽ có các file **main.tf**, **variables.tf**, **output.tf** và **role.tf** (chứa resource tạo permision cho services, tuỳ resource có cần hay không). Về công dụng của các file thì mình đã nêu ở [phần 4.1](../../4-knowledgeTerraform/4.1-overview/_index.vi.md).

**Vậy làm sao để bạn có thể tìm thấy các resouces cần thiết, các argument và atribute cho block?** Bạn hãy tìm nó tại đây: [aws provider registry docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs). 
Đây là document mới nhất thời điểm hiện tại mình viết workshop này, trên đây có tất cả thông tin để bạn có thể viết code terraform và tạo các services bạn muốn.



---
title : "Tạo một project hoàn chỉnh với Terraform và AWS services"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---

### Tổng quan

Với [Workshop 01](https://bibichannel.github.io/workshop-01/vi/) của mình là hướng dẫn bạn phát triển và triển khai tự động một trang web tĩnh trên AWS cloud với các dịch vụ Cloud9, S3, Codepiple, Cloudfront, Route53,...

Thì với Worksop 02 này chúng ta cùng tìm hiểu về cách làm sao có thể provisioning infracstructure trên aws dưới dạng mã bằng một công cụ mã nguồn mở là Terraform do HashiCorp phát triển, cùng với đó là cách kết hợp các dịch vụ serverless, CI/CD, mornitoring và management của AWS.

Cùng xem diagram dưới đây để hình dung ra bức tranh toàn cảnh của workshop này.

![IMAGE](/images/1-introduce/001-introduce.png)

### Nội dung
- [1. Giới thiệu](./1-introduce/)
- [2. Điều kiện tiên quyết](./2-prerequisites/)
- [3. Chuẩn bị repository phía gibhub](./3-setupGithub/)
- [4. Terraform và những điều cần biết](./4-knowledgeTerraform/)
- [5. Cung cấp cơ sở hạ tầng và kiểm tra ](./5-provisioning&Testing/)
- [6. Dọn dẹp tài nguyên](./6-cleanup/)
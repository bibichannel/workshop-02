---
title : "Git action"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---

Với việc provisioning infrastructure trên github action thì dễ hơn, bạn không cần phải chuẩn bị gì cả.

- Truy cập repository terraform bạn đã tạo ở [bước 3.1](../../3-setupGithub/3.1-createTerraformRepo/_index.vi.md).
- Chọn **Actions**, nhấn vào **Deploy Infracstructure** rồi chọn **Run workflow**.
- Chọn các tham số cần thiết cho nhu cầu của bạn và nhấn **run workflow**.

![image](/images/5-provisioning&Testing/5.2-githubAction/001-githubAction.png)

Bạn có thể truy cập để xem chi tiết, ở đây mình chỉ chạy **terraform plan**.

![image](/images/5-provisioning&Testing/5.2-githubAction/002-githubAction.png)

Chọn **apply** cho **Selecting actions for terraform** và chạy lại, check kết quả như ở [bước 5.1](../../5-provisioning&Testing/5.1-local/_index.vi.md).

{{%notice info%}}
Ở đây mình để chạy thử công. Nhưng trong thực tế thì sẽ chạy tự động mỗi khi code terraform được sửa và push. Bạn có thể thử thêm điều kiện trigger trong *.github/workflows/terraform-deploy.yml* file.

{{%/notice%}}
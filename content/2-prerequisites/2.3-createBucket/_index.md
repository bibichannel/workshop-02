---
title : "Create bucket"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

Chúng ta sẽ tạo 1 bucket dùng để lưu trữ state file cho terraform. Mình sẽ giải thích chi tiết hơn khi chúng ta vào [phần 4](../../4-knowledgeTerraform/)

1. Trong giao diện Create bucket
- **AWS reigon** ta chọn S3 bucket tại region mà ta đang làm lab. Hiện tại mình đang làm lab tại **N.Virginia (us-east-1)** region.
- **Bucket Type** trong khuôn khổ bài lab ta chọn **General purpose** là đủ.
- Nhập Bucket name, phải nhập tên duy nhất, bạn có thể chọn tùy ý ở đây mình tạo bucket với tên là `backend-tf-bibichannel`

{{%notice info%}}
AWS S3 có thể truy cập public và AWS S3 cung cấp cho chúng ta cách access vào bucket và object của nó bằng API REST.
Về cơ bản, đường dẫn cần phải tuân thủ DNS và không thể có hai tên miền giống nhau. Vì vậy, bucket names cần phải là duy nhất để truy xuất nhóm và đối tượng bằng điểm cuối REST API.

{{%/notice%}}

![S3](/images/2-prerequisites/2.3-createBucket/001-createBucket.png)

Ở dưới ta sẽ giữ nguyên cấu hình mặc định và click **Create bucket**.
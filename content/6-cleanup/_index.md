---
title : "Resource Cleanup"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

Hãy huỷ các tài nguyên lần lượt theo list dưới đây.

1. Sử dụng lệnh dưới đây nếu bạn sử dụng terraform ở local
```bash
terraform destroy -auto-approve
```
2. Chạy workflow với tham số **detroy** ở **Selecting actions for terraform** để huỷ infrastucture đã được provisioned.
3. Xoá repository nếu cần thiết.
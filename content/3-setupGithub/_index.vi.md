---
title : "Chuẩn bị github repository"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---

Trong workshop này ta sẽ cần 2 github repo:
1. Repository đầu tiên mình sẽ lưu terraform code để provisioning tới aws thông qua **git action**. Điều này sẽ giúp mình có thể downgrade version mỗi khi cấu hình được thay đổi bị lỗi thông qua việc quản lí version trên github. Và nó cũng giúp chúng ta cũng có thể teamwork được với nhau.
2. Repository thứ hai mình sẽ lưu source code của streamlit app, mỗi khi push code lên repo thì nó sẽ được push event thông qua webhook để Codepipeline được trigger, build và deploy lên ECS Fargate.


### Nội Dung
 - [Terraform IaC đến AWS Cloud](3.1-createTerraformRepo/)
 - [Streamlit application](3.2-createStreamlitRepo/)


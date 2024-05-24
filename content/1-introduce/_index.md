---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

![IMAGE](/images/1-introduce/001-introduce.png)

Nhìn vào architecture các bạn cũng đã thấy, đây là một project khá lớn với nhiều dịch vụ của AWS serivices đi kèm theo:
- Deploy application lên 2 AZ tăng tính HA và phân tải đến các ứng dụng qua Application Load Balancer (ALB).
- Tạo pipeline từ Codepipeline cho `streamlit` app, deploy lên dịch vụ serverless Elastic Container Service.
- Sử dụng Codebuild để build images và lưu giữ image trên Elastic Container Registry (ECR).
- Nhận thông báo trạng thái pipeline về email với EventBridge và Amazon SNS.
- Sử dụng Route53 để custom domain cho riêng mình.

Sẽ thế nào nếu bạn phải tương tác bằng tay để setup các dịch vụ trên? Hoặc là khi bạn chuyển giao project này cho người trong team, liệu người đó có setup đúng và chính xác những gì bạn đã document trước đó. Hay là khi sếp bạn thấy project bạn làm rất tốt nên muốn áp dụng kiến trúc này cho các project tiếp theo, thì việc setup một lượng lớn các dịch vụ như vậy khiến ta không thể nào không mắc sai lầm được.
Chính vì lẽ đó việc cung cấp cơ sở hạ tầng dưới dạng mã ra đời. Nó giúp chúng ta có thể nhanh chóng trong việc triển khai, hạn chế lỗi con người,...

Đây cũng chính là mục đích của mình khi làm bài viết này. Thông qua workshop này có thể giúp bạn có thể tự tin setup một dự án với terraform trên nền tảng AWS Cloud.

Ở đây mình sẽ có 2 repository trên github với các mục đích như sau:
1. Repository đầu tiên mình sẽ lưu terraform code để provisioning tới aws thông qua **git action**. Điều này sẽ giúp mình có thể downgrade version mỗi khi cấu hình được thay đổi bị lỗi thông qua việc quản lí version trên github. Và nó cũng giúp chúng ta cũng có thể teamwork được với nhau.
2. Repository thứ hai mình sẽ lưu source code của streamlit app, mỗi khi push code lên repo thì nó sẽ được push event thông qua webhook để Codepipeline được trigger, build và deploy lên ECS Fargate.

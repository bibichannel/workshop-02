---
title : "Create CodeStar Connection"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

Lại để ý một chút về artchitect, ta thấy có một repository của github thực hiện push những sự kiện thay đổi qua github để trigger Codepipeline.

Vì vậy ta cũng cần phải có một công cụ để có thể connect Github tới AWS. Và AWS biết điều đó và cung cấp cho chúng ta một công cụ có tên là **Codestar Connection**.

- Truy cập [CodePipeline](https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines?region=us-east-1).
- Chọn **Connections**
- Chọn **Create connection**

![image](/images/2-prerequisites/2.4-createCodeStar/001-createCodeStar.png)

- Trong phần **Select a provider**, tích chọn **Github**.
- Đặt **Connection name** là `workshop-02`.
- Chọn **Connect to GitHub**

![image](/images/2-prerequisites/2.4-createCodeStar/002-createCodeStar.png)

- Trong mục **GitHub Apps** ta click chọn **Install a new app**

![image](/images/2-prerequisites/2.4-createCodeStar/003-createCodeStar.png)

- Chuyển hướng url xuất hiện trang đăng nhập account github của bạn, hãy đăng nhập và uỷ quyền cho AWS. 

![image](/images/2-prerequisites/2.4-createCodeStar/004-createCodeStar.png)

- Sau khi tạo xong, ta sẽ lưu lại **ARN** của connection của chúng ta, để sử dụng trong quá trình viết code terraform.

![image](/images/2-prerequisites/2.4-createCodeStar/005-createCodeStar.png)

{{%notice info%}}
**Khoan đã !!!** Bài này sử dụng terraform để provisioning infrastructure trên AWS, vậy tại sao ta không viết code để tạo luôn services này?
Lúc đầu mình cũng có suy nghĩ này, nhưng khi ta viết code tạo **CodeStar Connection** thì khi tạo ra service này sẽ ở trạng thái **PENDING**, và ta sẽ cần vô tương tác trong AWS Console để nó connect thành công. 
Điều này sẽ ảnh hưởng tới việc tự động hoá, làm cho quá trình tự động hoá bị gián đoạn. Nên ta sẽ tạo service này trước để quá trình IaC được trơn tru.
{{%/notice%}}


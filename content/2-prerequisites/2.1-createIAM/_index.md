---
title : "Tạo User Groups và IAM User"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

Trước khi tạo **IAM User** ta sẽ tạo một **User groups** cấp policies cần thiết, việc tạo user groups giúp ta dễ dàng kiểm soát tập trung các user, phân loại user theo vai trò trong tổ chức, cũng như việc cấp quyền, thêm bớt các user một cách linh hoạt.

Tiếp theo IAM User gán vào User groups chúng ta vừa tạo là xong. User mới này sẽ kế thừa các quyền từ User groups

Chúng ta sẽ sử dụng user này để thực hiện uỷ quyền cho terraform dưới local của chúng ta, giúp ta có thể sử dụng terraform để test cấu hình trên aws.

Truy cập vào [IAM console](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/home):
- Chọn **User groups**.
- Nhấn vào **Create group**.

![image](/images/2-prerequisites/2.1-createIAM/001-createIAM.png)
![image](/images/2-prerequisites/2.1-createIAM/001.1-createIAM.png)

- Chúng ta sẽ tạo group có tên là `DevOpsGroup`.

![image](/images/2-prerequisites/2.1-createIAM/002-createIAM.png)

- Ở phần **Attach permissions policies** chọn policies `AdministratorAccess`.

![image](/images/2-prerequisites/2.1-createIAM/003-createIAM.png)

- Nhấn **Create user group**.

![image](/images/2-prerequisites/2.1-createIAM/004-createIAM.png)

{{% notice note%}}
**Việc chọn policy AdministratorAccess**. Vì workshop này mục tiêu không phải là về bảo mật nên để cho nhanh mình sẽ sử dụng quyền này. Mình không khuyến khích các bạn sử dụng quyền trên vì nó có đặc quyền khá rộng. Hãy tự tạo cho mình policy với các quyền giới hạn cần thiết cho việc bảo mật bạn nhé.

{{% /notice %}}

- Tiếp theo ta sẽ tiến hành tạo **IAM User**.
- Nhấn chọn **User** và click vào **Create user**.
- Nhập tên user là `terraform`.
- Chọn **Next**.

![image](/images/2-prerequisites/2.1-createIAM/005-createIAM.png)
![image](/images/2-prerequisites/2.1-createIAM/005.1-createIAM.png)

- Ở phần **Set permissions** chọn options **Add user to group**.
- Chọn `DevOpsGroup` ta mới tạo vừa nãy.
- Chọn **Next** và chọn **Create User**.

![image](/images/2-prerequisites/2.1-createIAM/006-createIAM.png)

- Giao diện sau khi tạo xong user.

![image](/images/2-prerequisites/2.1-createIAM/007-createIAM.png)

- Tiếp theo ta sẽ tạo **Access key** cho user để sử dụng user này vơi AWS CLI.
- Click vào `terraform` user ta vừa tạo, chọn mục **Sercurity Credentials**.

![image](/images/2-prerequisites/2.1-createIAM/008-createIAM.png)

- Cuộn chuột xuống phần **Access keys** ta nhấn **Create access keys**.

![image](/images/2-prerequisites/2.1-createIAM/009-createIAM.png)

- **Use case** ta chọn **Command Line Interface (CLI)**.

![image](/images/2-prerequisites/2.1-createIAM/010-createIAM.png)

- Tích **I understand the above recommendation and want to proceed to create an access key** và chọn **Next**.

![image](/images/2-prerequisites/2.1-createIAM/011-createIAM.png)

- Ở phần **Set description tag** nhập `Check the terraform code call aws api`
- Chọn **Create access key**

![image](/images/2-prerequisites/2.1-createIAM/012-createIAM.png)

- Chọn **Download access key .csv**

![image](/images/2-prerequisites/2.1-createIAM/013-createIAM.png)

- Vậy là bạn đã hoàn thành xong bước tạo **User groups** và **IAM user**, hãy đến với bước làm tiếp theo nhé.
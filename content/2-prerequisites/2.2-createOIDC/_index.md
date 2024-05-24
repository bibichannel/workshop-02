---
title : "Create OIDC"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

Trong architecture của chúng ta sử dụng github repository để thực hiện việc provisioning infrastructure lên AWS Cloud services.
Để github có quyền có thể làm điều này chúng ta sẽ cần phải có credentials cho github.

![image](/images/2-prerequisites/2.2-createOIDC/000-createOIDC.png)

Cách đơn giản nhất như sử dụng local là ta sẽ sử dụng access key của user account ta vừa tạo cấp cho github trong quá trình chạy git action.
Nhưng điều này ta cần phải tạo secret trong github, đưa credentails user aws của chúng ta cho bên thứ 3 giữ. Trong quá trình đó nếu có các policies quá 90 ngày cần phải reset password cho user thì cũng rất là tốn thời gian đi cập nhật lại secret key trong github.

Bởi vậy ta sẽ sử dụng một phương pháp cấp quyền khác, mới hơn. Nó sẽ giúp ta tạo một temporary credentials mỗi lần chạy git action, điều này giúp bảo mật và dễ dàng quản lí hơn. Đó là tạo một nhà cung cấp danh tính OpenID Connection (OIDC) trong IAM.

Để tạo OIDC trong IAM rất đơn giản.
- Trong giao diện IAM, chọn **Identity providers**.
- Chọn **Add provider**.

![image](/images/2-prerequisites/2.2-createOIDC/001-createOIDC.png)

- Trong phần **Configure provider** chọn **OpenID Connect**.
- Mục **Provider URL** ta nhập URL của IdP: `https://token.actions.githubusercontent.com`.
- Mục **Audience** ta nhập `sts.amazonaws.com`.
- Rồi chọn **Add provider**.

![image](/images/2-prerequisites/2.2-createOIDC/002-createOIDC.png)

- Đây là thông tin về **Identity providers** chúng ta vừa mới tạo. Hãy lưu lại Amazon Resource Name (ARN), ta sẽ cần nó cho việc tạo role. 

![image](/images/2-prerequisites/2.2-createOIDC/003-createOIDC.png)

Bạn có thể đọc thêm thông tin về: [Create an OpenID Connect (OIDC) identity provider in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)

Sau khi tạo nhà cung cấp danh tính IAM OIDC, bạn phải tạo một IAM role. Role này cho phép IdP của github yêu cầu temporary security credentials để truy cập vào AWS.

- Trong giao diện IAM, ta chọn **Roles**, rồi chọn **Create role**.

![image](/images/2-prerequisites/2.2-createOIDC/004-createOIDC.png)

- Tại **Select trusted entity**, ta tích vào **Custom trust policy**, rồi nội dung dưới đây vào editor.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": providerArn
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                },
                "StringLike": {
                    "token.actions.githubusercontent.com:sub": "repo:GitHubOrg/GitHubRepo:*"
                }
            }
        }
    ]
}
```

- Với phần tử `Principal`, sử dụng Amazon Resource Name (ARN) của OIDC IdP mà ta đã tạo phía trên.
- Với phần tử **Condition**, sử dụng điều kiện **StringEquals** để giới hạn quyền.
- Với phần tử StringLike sử dụng `repo:GitHubOrg/GitHubRepo:*` của bạn, ví dụ như mình để sẽ là `repo:bibichannel/terraform-project:*`. Chú ý repo này sẽ là tên repo mà bạn lưu code terrafrom. Đây sẽ là điều kiện để IAM có thể assume role cho github ở repository bạn muốn, đó là cách tốt nhất để hạn chế các thực thể có thể đảm nhận vai trò được liên kết với IAM IdP.

Bạn có thể đọc thêm thông tin về: [Configuring a role for GitHub OIDC identity provider](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp_oidc.html#idp_oidc_Create_GitHub).

Phần **Permissions policies** ta add permision `AdministratorAccess`. Vì để cho nhanh mình sẽ sử dụng quyền này, nhưng trong thực tế hãy áp dụng nguyên tắc đặc quyền tối thiểu để tăng tính bảo mật cho account của bạn.

![image](/images/2-prerequisites/2.2-createOIDC/005-createOIDC.png)

- Đặt tên cho role: `github-oidc-role`.
- Rồi chọn **Create role**.

![image](/images/2-prerequisites/2.2-createOIDC/006-createOIDC.png)

{{% notice note%}}
Với IAM Role cta đã tạo sẽ cấp cho repository trên github quyền truy cập vào tài nguyên AWS trong tài khoản của chúng ta. Và mình sẽ sử dụng role này trong quá trình setup github action. Hãy xem bước tiếp theo nhé.

{{% /notice %}}
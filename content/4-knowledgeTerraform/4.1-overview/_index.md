---
title : "Overview"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

### 1. Tổng quan về terraform
Terraform là một open-source của HashiCorp, chuyên dùng để provisioning infrastructure, ta chỉ việc viết code, rồi gõ một vài câu CLI đơn giản, nó sẽ tạo ra Infrastructure cho ta.

Ngôn ngữ Terraform sử dụng gọi là HashiCorp Configuration Language (HCL)

Flow của terraform sẽ như sau, ta viết code, xong ta gõ câu lệnh CLI, và đợi nó cung cấp infrastructure, sau khi nó tạo xong thì nó sẽ tạo ra một file state để lưu lại kiến trúc hạ tầng hiện tại của ta. State là một bản ghi của hạ tầng hiện tại mà Terraform đã quản lý. Nó được sử dụng để theo dõi các tài nguyên đã được tạo, sửa đổi hoặc xóa. State cũng giúp Terraform xác định những thay đổi cần thực hiện để đạt được trạng thái mong muốn của hạ tầng.
- State file: Mặc định, Terraform lưu trữ state trong một tệp có tên **terraform.tfstate** trong thư mục làm việc hiện tại. Tệp này chứa thông tin về tất cả các tài nguyên mà Terraform đang quản lý.

- State Backend: Terraform có thể lưu trữ state trên các dịch vụ lưu trữ từ xa như Amazon S3, Google Cloud Storage, Azure Blob Storage, hay Terraform Cloud. Điều này giúp chia sẻ state giữa các thành viên trong nhóm và giữ an toàn cho state.

### 2. Cách terraform connect với clouds

1. Terraform gửi **request** đến **public API** của clouds.
2. Ở phía clouds sẽ **authentication** các request này. Terraform sử dụng các **provider** để đính kèm thông tin trong quá trình authentication. Các provider như là google platform provier, aws provider, azure provider,…  
3. Để hỗ trợ quá trình authentication ta sẽ cần **services account (IAM)** để cung cấp các **permission**.

### 3. Terraform Lifecycle

![IMAGE](/images/4-knowledgeTerraform/4.1-overview/001-overview.png)

Khi làm việc với terraform thì ta cần nắm được 4 lệnh cơ bản này:

- **Terraform Init**: Dùng để khởi tạo **env ở local (initialization workspace)**. Lệnh này sẽ đọc các scrips mình đã tạo, kiểm tra version và download các API package cần thiết cho quá trình deploy. Chạy terraform init ở lần **đầu tiên** hoặc khi ta thêm một số **provider,** các **public/private module** vào bên trong **terraform code** của mình.
- **Terraform Plan**: So sánh trạng thái của môi trường **local** và trên **cloud** (cụ thể là so sánh trong states file). Đưa ra các execution-plan, như là các thay đổi trước hoặc sau khi deploy. Quá trình này chưa deploy bất cứ thứ gì lên clouds nhé.
- **Terraform Apply**: Thực hiện **deploy**, đẩy các thay đổi của source code **lên cloud**. Tạo ra **state files.**
- **Terraform Destroy**: Nghe tên là hiểu, **xóa** toàn bộ **infrastructure** đã deploy trước đó.

### 4. Các blocks trong terraform

![IMAGE](/images/4-knowledgeTerraform/4.1-overview/002-overview.png)

#### 1. Provider Block
**Provider block** được sử dụng để định nghĩa và cấu hình nhà cung cấp dịch vụ mà Terraform sẽ tương tác, như AWS, Azure, Google Cloud,...
#### 2. Terraform Block
**Terraform block** được sử dụng để cấu hình các thiết lập toàn cục của Terraform, như phiên bản yêu cầu của Terraform, backend, và các provider mặc định.
#### 3. Resource Block
**Resource block** được sử dụng để tạo và quản lý tài nguyên trong nhà cung cấp dịch vụ, như instance EC2, bucket S3,...
#### 4. Variable Block
**Variable block** được sử dụng để định nghĩa các biến đầu vào, cho phép người dùng tùy chỉnh các giá trị trong cấu hình Terraform.
#### 5. Module Block
**Module block** được sử dụng để gọi và sử dụng các module Terraform, giúp tái sử dụng cấu hình và tổ chức mã nguồn tốt hơn.
#### 6. Data Block
**Data block** được sử dụng để truy vấn thông tin về các tài nguyên đã tồn tại hoặc các dịch vụ từ nhà cung cấp dịch vụ, mà không tạo ra hoặc thay đổi bất kỳ tài nguyên nào.
#### 7. Locals Block
**Locals block** được sử dụng để định nghĩa các biến cục bộ, giúp đơn giản hóa và tái sử dụng các giá trị phức tạp trong cấu hình Terraform.
#### 8. Output Block
**Output block** được sử dụng để định nghĩa các biến đầu ra (output variables), cung cấp thông tin hữu ích sau khi triển khai hạ tầng. Các đầu ra này có thể bao gồm ID của tài nguyên, địa chỉ IP, v.v.
#### 9. Backend Block
**Backend block** được sử dụng để định nghĩa và cấu hình backend, nơi lưu trữ trạng thái của Terraform.

{{%notice info%}}
Các block này sẽ được viết trong các tệp terraform có đuôi **.tf**, dưới đây sẽ là các tệp **.tf** phổ biến cho việc biểu diễn code terraform trở nên có tổ chức hơn.

{{%/notice%}}


### 5. Các tệp trong terraform
#### 1. main.tf
**main.tf** là tệp chính trong cấu hình Terraform, chứa định nghĩa các tài nguyên (resources block) và các cấu hình cần thiết để triển khai cơ sở hạ tầng (locals block, terraform block, module block,...).

#### 2. variables.tf
**variables.tf** được sử dụng để định nghĩa các biến đầu vào (variables block) mà có thể được tham chiếu trong các tệp cấu hình khác. Điều này giúp tái sử dụng và quản lý cấu hình dễ dàng hơn.

#### 3. output.tf
**output.tf** được sử dụng để định nghĩa các biến đầu ra (output block), cung cấp thông tin hữu ích sau khi triển khai hạ tầng. Các đầu ra này có thể bao gồm ID của tài nguyên, địa chỉ IP,...

#### 4. vars.tfbackend
**vars.tfbackend** thường được sử dụng để cấu hình backend cho Terraform, nơi trạng thái của Terraform sẽ được lưu trữ (như trong S3, Azure Blob Storage, v.v.). Tệp này thường được bao gồm trong lệnh terraform init để thiết lập backend. Và trong workshop này thì mình sẽ cấu hình backend lưu trữ trên S3.

#### 5. terraform.tfvars
**terraform.tfvars** được sử dụng để cung cấp các giá trị mặc định cho các biến đầu vào đã được định nghĩa trong variables.tf. Tệp này giúp tách biệt cấu hình và giá trị biến, cho phép dễ dàng thay đổi cấu hình mà không cần chỉnh sửa tệp cấu hình chính.

#### 6. providers.tf
**providers.tf** được sử dụng để định nghĩa các nhà cung cấp dịch vụ (providers block) mà Terraform sẽ tương tác, như AWS, Azure, Google Cloud, v.v. Tệp này cũng có thể chứa cấu hình cho các nhà cung cấp, như vùng (region), thông tin xác thực (credentials), v.v.


{{%notice info%}}
Việc tách biệt các cấu hình này giúp duy trì và quản lý dự án Terraform dễ dàng hơn, đảm bảo cấu hình rõ ràng và có tổ chức.

{{%/notice%}}

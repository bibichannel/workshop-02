---
title : "Streamlit application"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

#### Source code bạn dùng cho phần này sẽ ở đây
 - [streamlit-app](https://github.com/bibichannel/streamlit-app).

#### Bạn có thể fork thẳng từ repo của mình về
- Với **Repository name** là tên trùng với tên bạn đặt cho biến **YOUR_GITHUB_REPOSITORY** ở trong file **terraform-deploy.yml** của repository bạn tạo ở [bước 3.1](../../3-setupGithub/3.1-createTerraformRepo/_index.vi.md)

- Nhấn **Create fork**.

#### Chúng ta cùng tìm hiểu sơ qua các thành phần trong repository này
Cây thư mục của repo này như sau:

![IMAGE](/images/3-setupGithub/3.2-createStreamlitRepo/001-createStreamlitRepo.png)

1. **Streamlit** là một open-source app framework giúp ta tạo nhanh một website thuần tuý bằng python không cần có kinh nghiệm front-end.
2. **Dockerfile** là một file để build source code thành image với nội dung như sau:
```Dockerfile
FROM public.ecr.aws/docker/library/python:3.9.19-slim-bullseye

WORKDIR /app

COPY main.py requirements.txt /app/

RUN pip3 install -r requirements.txt

EXPOSE 8501

ENTRYPOINT ["streamlit", "run", "main.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

- Ở đây mình sẽ lấy image base được build từ public registry của aws.
**Nếu sử dụng images từ docker registry có được không?** Thì trong quá trình mình làm, mình đã sử dụng images được lấy từ **docker regsitry**, nhưng mình nhận ngay một lỗi như sau: 
    - "*toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit*"

- Vì docker giới hạn việc pull images từ public registry của nó, nó sử dụng địa chỉ IP để xác thực người dùng và giới hạn tốc độ kéo dựa trên địa chỉ IP. Đối với người dùng ẩn danh, giới hạn tốc độ được đặt thành 100 lần pull mỗi 6 giờ cho mỗi địa chỉ IP.
Nên ta sẽ sử dụng Amazon ECR public registry để pull image.

3. **buildspec.yml** là file cho **AWS CodeBuild** chạy build. Bạn có thể tham khảo thêm các thành phần trong **buildspec file** tại [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html).
- File có nội dung như sau:
```yml
version: 0.2

phases:
  pre_build:
    commands:
      # Retrieve an authentication token and authenticate your Docker client to your registry
      - RESULT=$(aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $REPOSITORY_URL)
      - echo $RESULT
  build:
    commands:
      # Build the Docker image
      - docker build -t streamlit-app:latest .
      - SHORT_VERSION=$(echo "$CODEBUILD_RESOLVED_SOURCE_VERSION" | cut -c 1-8)
      - docker tag streamlit-app:latest $REPOSITORY_URL:$SHORT_VERSION
      - docker tag streamlit-app:latest $REPOSITORY_URL:latest
      - echo $SHORT_VERSION
      - echo $REPOSITORY_URL
  
  post_build:
    commands:
      # Push docker images to ECR
      - docker push $REPOSITORY_URL:latest
      - docker push $REPOSITORY_URL:$SHORT_VERSION
      - printf '[{"name":"streamlit-app","imageUri":"%s"}]' $REPOSITORY_URL:$SHORT_VERSION > imagedefinitions.json

artifacts:
  files: imagedefinitions.json
```

- Trong quá trình build sẽ có 3 phase:
  - **pre_build**: thực hiện login vào **AWS Elastic Container Registry**.
  - **build**: build image và gắn tags. Mình sẽ gắn 2 tags cho image được build ra.
  - **post_build**: push image lên **AWS ECR** và tạo artifact **imagedefinitions.json** chứa thông tin của **container name** và **uri container**, dùng cho việc deploy lên ECS.
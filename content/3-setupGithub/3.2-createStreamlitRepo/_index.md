---
title : "Streamlit application"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

#### The source code for this part is available here:

 - [streamlit-app](https://github.com/bibichannel/streamlit-app).

#### You can fork it directly from my repository:
- With the **Repository name** matching the one you set for the **YOUR_GITHUB_REPOSITORY** variable in the **terraform-deploy.yml** file of the repository you created in [step 3.1](../../3-setupGithub/3.1-createTerraformRepo/_index.md)

- Click **Create fork**.

#### Let's briefly explore the components in this repository
The directory structure of this repo is as follows:

![IMAGE](/images/3-setupGithub/3.2-createStreamlitRepo/001-createStreamlitRepo.png)

1. **Streamlit** is an open-source app framework that allows you to create websites quickly using Python without needing frontend experience.
2. **Dockerfile** is a file to build the source code into an image with the following content:
```Dockerfile
FROM public.ecr.aws/docker/library/python:3.9.19-slim-bullseye

WORKDIR /app

COPY main.py requirements.txt /app/

RUN pip3 install -r requirements.txt

EXPOSE 8501

ENTRYPOINT ["streamlit", "run", "main.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

- Here I'm using a base image built from the AWS public registry.
**Can I use images from the docker registry?** During my work, I tried to use images from the **docker registry**, but I encountered an error immediately:
    - "*toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit*"

- Docker limits the pulling of images from its public registry, it uses the IP address to authenticate users and limits the pulling speed based on the IP address. For anonymous users, the pulling limit is set to 100 pulls every 6 hours per IP address.
So, we will use the Amazon ECR public registry to pull the image.

3. **buildspec.yml** is the file for **AWS CodeBuild** to run the build. You can refer to more components in the **buildspec file** at [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html).
- The file has the following content:
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

- During the build process, there will be 3 phases:
  - **pre_build**: performs login to the **AWS Elastic Container Registry**.
  - **build**: builds the image and tags it. I will assign 2 tags to the built image.
  - **post_build**: pushes the image to **AWS ECR** and creates an artifact **imagedefinitions.json** containing information about the **container name** and **container URI**, used for deploying to ECS.
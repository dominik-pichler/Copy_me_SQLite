# Process for CI/CD Pipeline

## 1. Create a Dockerfile: 
Define your container environment and dependencies in a Dockerfile. This file specifies how to build your application into a container image.

## 2. Setup CI/CD Pipeline:

* **Trigger on Push:** <br> 
  Configure your CI/CD tool to trigger a pipeline whenever code (including Dockerfile) is pushed to the repository.

* **Build Stage**:<br> Build the Docker image inside your CI/CD pipeline using the Docker build command. This will create a new image with the latest changes.

* **Push Image**: <br> Push the newly built image to a container registry (e.g., Docker Hub, AWS ECR, etc.) where it can be accessed by the target machine.

* **Deploy Stage**:<br> Use SSH or a deployment script to connect to the target machine from your CI/CD pipeline. Execute commands to pull the latest image and restart the container on the target machine.


## 3. Deployment Script
Here’s a simplified example using SSH and Docker commands in a deployment script (assuming you have SSH access and Docker installed on the target machine):

```
#!/bin/bash

# Replace these variables with your actual values
TARGET_HOST="your_target_machine_ip_or_hostname"
SSH_USER="your_ssh_username"
IMAGE_NAME="your_dockerhub_username/myapp"
IMAGE_TAG="latest"

# SSH into the target machine and perform deployment steps
ssh $SSH_USER@$TARGET_HOST << EOF
  docker pull $IMAGE_NAME:$IMAGE_TAG
  docker stop myapp || true   # Stop the existing container if running
  docker rm myapp || true     # Remove the existing container if exists
  docker run -d -p 8080:80 --name myapp $IMAGE_NAME:$IMAGE_TAG
EOF

```

## 4. Secure Authentication: 
Ensure secure handling of credentials and SSH keys in your CI/CD tool to securely connect and deploy to the target machine.

## 5. Testing and Monitoring: 
Implement testing and monitoring steps in your pipeline to ensure the deployment is successful and the application is running correctly after each deployment

___
# CI/CD Tool Configuration Examples

## Github Actions:
```
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      
      - name: Build Docker image
        run: docker build -t your_dockerhub_username/myapp:latest .
      
      - name: Push Docker image
        run: |
          docker login -u your_dockerhub_username -p ${{ secrets.DOCKERHUB_TOKEN }}
          docker push your_dockerhub_username/myapp:latest
      
      - name: Deploy to target machine
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.TARGET_HOST }}
          username: ${{ secrets.SSH_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            docker pull your_dockerhub_username/myapp:latest
            docker stop myapp || true
            docker rm myapp || true
            docker run -d -p 8080:80 --name myapp your_dockerhub_username/myapp:latest

```

## Gitlab CI/CD: 
```
image: docker:19.03.12

stages:
  - build
  - deploy

build:
  stage: build
  script:
    - docker build -t your_dockerhub_username/myapp:latest .
    - docker login -u your_dockerhub_username -p $DOCKER_PASSWORD
    - docker push your_dockerhub_username/myapp:latest

deploy:
  stage: deploy
  script:
    - ssh $SSH_USER@$TARGET_HOST "docker pull your_dockerhub_username/myapp:latest"
    - ssh $SSH_USER@$TARGET_HOST "docker stop myapp || true"
    - ssh $SSH_USER@$TARGET_HOST "docker rm myapp || true"
    - ssh $SSH_USER@$TARGET_HOST "docker run -d -p 8080:80 --name myapp your_dockerhub_username/myapp:latest"

```

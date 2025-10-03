# 🚀 DevOps Hands-On Exercise: Docker + Jenkins 

## Objective
In this exercise, you will:
1. Containerize a simple web application using **Docker**.
2. Create a **Jenkins pipeline** to build and push the image to **Docker Hub private registry**.
3. Deploy the application on an **AWS EC2 instance** using Docker.

---

## Tasks

### 1. Dockerfile
- Write a **Dockerfile** that:
  - Uses an appropriate base image.
  - Copies your application files into the container.
  - Exposes the required port.
  - Runs the service in the foreground.

## index file that will be deployed:
<!DOCTYPE html>
<html>
<head>
    <title>My Custom Nginx Page</title>
</head>
<body>
    <h1>Hello from Nginx inside Docker!</h1>
</body>
</html>





### 2. Build the Image
Build your Docker image locally with a unique tag:
```bash
docker build -t <your-dockerhub-username>/<image-name>:<tag> .
```

### 3. Jenkins Pipeline
Create a **Jenkinsfile** that performs the following:
- Build the Docker image.
- Log in to your **Docker Hub private registry** using Jenkins credentials.
- Push the image to Docker Hub.

### 4. Deployment Stage
In your Jenkins pipeline, add a deployment stage that will:
1. SSH into your **AWS EC2 instance**.  
   ⚠️ *If you don't have an EC2 machine, use a **dummy IP and key** in your pipeline for simulation.*
2. Log in to Docker Hub from the EC2 instance.
3. Pull the image you just pushed.
4. Stop and remove any old container with the same name.
5. Run a new container

---

## Deliverables
- A working **Dockerfile**.
- A complete **Jenkinsfile** with build, push, and deploy stages.
- Screenshots of:
  - Successful Jenkins pipeline run.
  - The running container on your EC2 instance (or dummy simulation if no EC2).  
  - Accessing the application from the EC2 public IP in a browser.
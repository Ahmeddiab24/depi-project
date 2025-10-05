# depi-project

Complete DevOps CI/CD Project Explanation
Project Concept
an automated CI/CD (Continuous Integration/Continuous Deployment) pipeline that:

Takes code from GitHub
Builds a Docker image
Pushes it to Docker Hub
Deploys it automatically to an AWS EC2 server

Every time you push code to GitHub, Jenkins automatically builds, tests, and deploys your application.

How Everything Works Together
Workflow:

1) Developer pushes code to GitHub

You commit changes to index.html or Dockerfile
Push to main branch


2) Jenkins detects the change

Polls GitHub or receives webhook
Clones repository


3) Build Stage

Jenkins reads Dockerfile
Builds Docker image locally
Tags it as ahmeddiab23/nginx-custom:1.0


4) Push Stage

Authenticates with Docker Hub
Uploads image to the Docker Hub account
Now accessible from anywhere


5) Deploy Stage

Connects to EC2 via SSH
Stops old container
Pulls new image
Starts fresh container
Application is now live



The Result:

Users visit http://ec2_ip
Nginx serves the index.html


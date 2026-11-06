# AWS CI/CD Pipeline with Jenkins, Docker & Automatic Rollback

## Project Overview
This project automates application deployment using Jenkins, GitHub, Docker, and AWS EC2.

Whenever code is pushed to GitHub, Jenkins automatically triggers the pipeline, builds a Docker image, and deploys the application to AWS EC2.

If the new deployment fails its health check, the pipeline attempts to restore the previous working Docker image.

## Architecture

GitHub → GitHub Webhook → Jenkins → Docker Build → AWS EC2 → Application

## Technologies Used
- AWS EC2
- Ubuntu Linux
- Jenkins
- Git & GitHub
- Docker
- Shell Scripting
- SSH

## Project Features
1. Automatic pipeline trigger using GitHub Webhook.
2. Source code checkout from GitHub.
3. Docker image build with unique build numbers.
4. Local application deployment.
5. Automatic deployment to AWS EC2 using SSH.
6. Docker image versioning.
7. Automatic rollback to the previous image when deployment health check fails.

## Deployment Workflow
1. Developer pushes code to GitHub.
2. GitHub Webhook triggers Jenkins.
3. Jenkins checks out the source code.
4. Jenkins builds a Docker image.
5. Jenkins deploys the application locally.
6. Docker image is transferred to AWS EC2.
7. Application is deployed on EC2.
8. Health check verifies the deployment.
9. If the health check fails, the previous image is restored.

## Testing
- Successful deployment tested.
- GitHub webhook trigger tested.
- AWS EC2 deployment tested.
- Failure scenario simulated.
- Automatic rollback verified through Jenkins Console Output.

## Learning Outcomes
- CI/CD pipeline implementation.
- Jenkins pipeline scripting.
- Docker image management.
- AWS EC2 deployment.
- SSH-based remote deployment.
- Deployment failure handling and rollback.

## Author
Shubham Asalkar

## Project Repository
https://github.com/shubhamasalkar02/aws-cicd-jenkins-pipeline.git

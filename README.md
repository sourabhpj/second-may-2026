End-to-End CI/CD Pipeline: Jenkins, Docker, and Kubernetes
​This repository contains the complete automation workflow for a custom Nginx application. The pipeline automates everything from code commit to deployment on a Kubernetes cluster.
​🏗️ Project Architecture
​Developer pushes code to GitHub.
​Jenkins triggers a build upon detecting changes.
​Docker builds a new image and pushes it to Docker Hub.
​Kubernetes (Minikube) pulls the new image and updates the deployment.
​User accesses the application via a Public IP.
​🛠️ Tech Stack
​Cloud Provider: AWS (EC2 t2.medium)
​CI/CD Tool: Jenkins
​Containerization: Docker
​Orchestration: Kubernetes (Minikube)
​Web Server: Nginx
​📝 Implementation Steps
​1. Environment Setup
​Launched an AWS EC2 Ubuntu instance.
​Installed Docker, Jenkins, and Minikube.
​Configured Security Groups to allow inbound traffic on port 8080 (Jenkins) and 8081 (App).
​2. Application & Dockerization
​Developed a custom index.html file.
​Created a Dockerfile to package the HTML with Nginx.
​Configured Jenkins to build and push the image to Docker Hub (sourabhpj94/my-nginx-image).
​3. Kubernetes Orchestration
​Deployment: Defined deployment.yaml to manage application replicas.
​Service: Defined service.yaml as a NodePort to expose the app.
​Applied configurations using kubectl apply.
​🛠️ Real-World Challenges Solved
​A key part of this project was troubleshooting production-level issues:
​Path & Typo Correction: Resolved a does not exist error caused by a filename typo (deplyoment.yaml vs deployment.yaml).
​Permission Hardening: Fixed permission denied errors by granting the Jenkins user access to Kubernetes certificates (.kube/config and .minikube certs).
​Authentication Bypass: Fixed HTML redirect/login errors during deployment by using the --validate=false flag.
​📊 Final Results
​Pipeline Status: Build #29 completed successfully.
​Deployment: Pods are in a Running state.
​Live Access: The application is successfully reachable at http://43.205.242.185:8081.
​How to Access
​To view the live output from the EC2 terminal, run:

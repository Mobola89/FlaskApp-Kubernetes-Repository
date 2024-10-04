## **Simple Flask Web App** ##

**Project Overview**

This project demonstrates the deployment of a simple Flask web application to a Kubernetes cluster using Kind (Kubernetes in Docker). It also automates the CI/CD process using GitHub Actions and incorporates basic security best practices.

**Application Name**

simple-webapp-flask

**Goals:**

- Deploy the Flask application to a Kubernetes cluster.

- Automate the CI/CD process with GitHub Actions.

- Implement basic security practices.


 ## **Table of Contents**
  
-Project Overview

-Setup Instructions

-Kubernetes Deployment

-CI/CD Pipeline

-Security Best Practices

-Technologies Used

-Project Summary

-Contributing

### **Setup Instructions**

**Prerequisites:**

- Docker installed on your machine.

- Kubernetes in Docker (Kind) installed for local Kubernetes clusters.

- GitHub account with access to create repositories and push Docker images to GitHub Packages.

**1. Clone the Repository**
   
"git clone https://github.com/mmumshad/simple-webapp-flask.git"

"cd simple-webapp-flask"

**2. Create Docker Image**

Dockerfile: A Dockerfile is already created in the repository, which defines how the Flask application is containerized.

**Build and Push Docker Image**:

- Build the Docker image:

"docker build -t ghcr.io/your-username/simple-webapp-flask:latest ."

- Push the Docker image to GitHub Packages:

"docker push ghcr.io/your-username/simple-webapp-flask:latest"

**3. Set Up Kubernetes Cluster (Using Kind)**
   
- Create a Kind cluster:

"kind create cluster"

**5. Deploy the Application on Kubernetes**

- Apply the deployment and service manifests:

"kubectl apply -f deployment.yaml"

"kubectl apply -f service.yaml"

**6. Verify the Deployment**
- Check the status of pods:

"kubectl get pods"

- Check the status of services:

"kubectl get services"

### **Kubernetes Deployment**

**Deployment Configuration:**

- 2 replicas of the Flask application will be running.
The service type is set as NodePort to allow external access.

**deployment.yaml**:

apiVersion: apps/v1

kind: Deployment

metadata:

  name: simple-webapp-flask-deployment
  
spec:

  replicas: 2
  
  selector:
  
    matchLabels:
    
      app: simple-webapp-flask
      
  template:
  
    metadata:
    
      labels:
      
        app: simple-webapp-flask
        
    spec:
    
      containers:
      
      - name: simple-webapp-flask-container
      
        image: ghcr.io/your-username/simple-webapp-flask:latest
        
        ports:
        
        - containerPort: 8080
        
        securityContext:
        
          runAsNonRoot: true
          
          runAsUser: 1000
          
        env:
        
        - name: FLASK_ENV
        
          value: production

**service.yaml**:


apiVersion: v1

kind: Service

metadata:

  name: simple-webapp-flask-service
  
spec:

  selector:
  
    app: simple-webapp-flask
    
  type: NodePort
  
  ports:
  
  - protocol: TCP
  - 
    port: 80
    
    targetPort: 5000
    
    nodePort: 30080
    
### **CI/CD Pipeline**

**GitHub Actions CI/CD:**

The CI/CD pipeline is configured using **GitHub Actions**. It automatically:

Builds the Docker image.

Pushes the image to GitHub Packages.

Deploys the application to the Kubernetes cluster.

The GitHub Actions workflow can be found in .github/workflows/**ci-cd.yml.**


name: CI/CD Pipeline

on:

  push:
  
    branches:
    
      - main
      

jobs:

  build:
  
    runs-on: ubuntu-latest
    
    steps:
    
    - name: Checkout code
    
      uses: actions/checkout@v2
      

    - name: Log in to GitHub Packages
    
      run: echo ${{ secrets.GITHUB_TOKEN }} | docker login ghcr.io -u ${{ github.actor }} --password-stdin

    - name: Build Docker image
    
      run: docker build -t ghcr.io/your-username/simple-webapp-flask:latest .

    - name: Push Docker image
    
      run: docker push ghcr.io/your-username/simple-webapp-flask:latest

  deploy:
  
    runs-on: ubuntu-latest
    
    needs: build
    
    steps:
    
    - name: Set up kubectl
    
      uses: azure/setup-kubectl@v1
      
      with:
      
        version: 'latest'

    - name: Deploy to Kubernetes
    
      run: |
      
        kubectl apply -f deployment.yaml
        
        kubectl apply -f service.yaml
        
### **Secrets Configuration:**

In the repository, add a GitHub secret for the GitHub Personal Access Token:

Go to Settings > Secrets > Actions.

Add a new secret named FLASKAPP_TOKEN.

### **Security Best Practices**

- Non-root user: The container runs the Flask app as a non-root user (runAsUser: 1000).
  
- Environment variables: Configured FLASK_ENV to production for deployment.
  
- Basic authentication: Secured access to GitHub Packages using a Personal Access Token.
  
### **Technologies Used**

- Flask: A lightweight Python web framework.
  
- Docker: Containerization platform for building and shipping application images.
  
- Kubernetes: An orchestration tool for deploying, managing, and scaling applications.
  
- Kind: A tool to run Kubernetes clusters using Docker.
  
- GitHub Actions: CI/CD service to automate workflows and deployments.
  
- GitHub Packages: Container registry to store Docker images.
  
### **Project Summary**

This project:

- Containerizes a simple Flask web application.

- Deploys the application to a local Kubernetes cluster using Kind.

- Implements automated CI/CD pipelines with GitHub Actions.
  
- Enforces basic security practices, including running the container as a non-root user and securing environment variables.
- 
### **Contributing**
  
Contributions are welcome! Feel free to open issues or submit pull requests.

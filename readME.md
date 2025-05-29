## Overview
This Jenkins pipeline automates the CI/CD process for the NGINX-based frontend of the expense project. It builds a Docker image, pushes it to AWS ECR, and deploys it to an EKS Kubernetes cluster using Helm.

## Pipeline Details
Agent
Runs on Jenkins node labeled AGENT-1.

Pipeline Options
Timeout set to 30 minutes.

Concurrent builds are disabled to avoid conflicts.

Environment Variables
DEBUG – Debug mode enabled.

AWS Region: us-east-1

AWS Account ID: return value from shared library

Project: expense

Environment: dev

Component: frontend

APP_VERSION – Dynamically fetched during pipeline run (used globally).

## Pipeline Stages
1. Read the Version
Clones the GitHub repo jenkins-frontend.

Uses the latest Git tag as the application version (APP_VERSION).

This version is used for tagging Docker images and Helm charts.

2. Building Docker Image
Logs in to AWS ECR using aws ecr get-login-password.

Builds Docker image tagged as:

php-template
Copy
Edit
<account_id>.dkr.ecr.<region>.amazonaws.com/<project>/<environment>/<component>:<APP_VERSION>
Pushes the Docker image to AWS ECR.

3. Deployment
Updates the local kubeconfig for EKS cluster (expense-dev).

Changes values.yaml to replace the image tag placeholder (IMAGE_VERSION) with the actual APP_VERSION.

Runs helm upgrade --install to deploy or upgrade the frontend Helm chart in Kubernetes.

### Post Actions
Cleans workspace (deleteDir()) after every run.

Prints success or failure messages.

Requirements & Prerequisites
Jenkins agent labeled AGENT-1.

AWS credentials configured in Jenkins with ID aws-creds.

Access to the EKS cluster.

Helm installed on Jenkins agent.

Docker installed on Jenkins agent.

Git installed on Jenkins agent.

## How to Use
Push your frontend changes with appropriate Git tags.

Trigger Jenkins pipeline manually or on GitHub push.

Watch the pipeline clone repo, build Docker image, push to ECR, and deploy to EKS.

Monitor the deployment status in Jenkins logs and Kubernetes cluster.
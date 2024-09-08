# RegTech CI/CD Setup Guide

## Step 1: Fork and Clone Repository
1. Fork this repository to your GitHub account.
2. Clone the forked repository to your local machine.

## Step 2: Create Self-Hosted Runner
1. Go to the "Settings" tab of your forked repository.
2. Navigate to "Actions" in the left sidebar.
3. Select "Runners" and follow the prompts to create a runner on your chosen VM.

## Step 3: Install Dependencies on Runner
Install the following on your self-hosted runner:

1. Docker
2. AWS CLI
   - Run `aws configure` to set up AWS access
3. Trivy 
   - Run 
     ```
     sudo apt-get install wget apt-transport-https gnupg lsb-release
     wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
     echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
     sudo apt-get update
     sudo apt-get install trivy
     ```
4. SonarQube
   - Run SonarQube server in a container:
     ```
     docker run -d -p 9000:9000 sonarqube:lts-community
     ```
   - Ensure TCP port 9000 is open on your VM

## Step 4: Set Up Docker Repository
Create a Docker repository and update the `CICD-pipeline.yml` file with your `username/repository:tag`.

## Step 5: Configure Repository Secrets
Add the following secrets to your repository:

- `DOCKERHUB_USERNAME`: Your DockerHub username
- `DOCKERHUB_TOKEN`: Generate this from DockerHub settings
- `SONAR_TOKEN`: SonarQube authentication token
- `SONAR_HOST_URL`: `http://<runner-public-ip>:9000`
- `KUBE_CONFIG`: Base64-encoded Kubernetes config (obtain from EKS cluster through the cli)
  ```
  base64 ~/.kube/config
  ```
## Step 5: Commit and push to trigger the pipeline.
this should build and deploy to the cluster and can be accessed from the cluster loadbalancer:8080 or nodeip:nodeport if load balancer isnt up yet.
you can check again with `kubectl get svc -n (your-namespace)`

![alt text](<Screenshot 2024-09-07 at 18.58.30.png>)


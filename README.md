# Flask_Sample
# Deployment Using GitHub Actions

This repository uses GitHub Actions for automated deployment to an EC2 instance. Follow these steps to set up deployment:

## Step-by-Step Deployment Guide

### 1. Fork or Clone Repository

Clone or fork this repository to your local machine:

```bash
git clone <repository_url>
cd <repository_name>


Certainly! Here's an example README.md file that provides step-by-step instructions on how to deploy a project using GitHub Actions. Additionally, I'll include a YAML configuration file for the GitHub Actions workflow for deployment.

README.md:
markdown
Copy code
# Deployment Using GitHub Actions

This repository uses GitHub Actions for automated deployment to an EC2 instance. Follow these steps to set up deployment:

## Step-by-Step Deployment Guide

### 1. Fork or Clone Repository

Clone or fork this repository to your local machine:

```bash
git clone <repository_url>
cd <repository_name>

2. Configure Secrets in Repository
Go to your GitHub repository > Settings > Secrets.
Add the following secrets:
EC2_SSH_KEY: Contents of your SSH private key.
HOST_DNS: Your EC2 instance's DNS or IP address.
USERNAME: Username for SSH access to the EC2 instance.
3. Modify GitHub Actions Workflow File
In .github/workflows/deploy.yml, ensure the following values are correctly set:

SSH_PRIVATE_KEY: Set to ${{ secrets.EC2_SSH_KEY }}.
REMOTE_HOST: Set to ${{ secrets.HOST_DNS }}.
REMOTE_USER: Set to ${{ secrets.USERNAME }}.
Verify other parameters like REMOTE_PORT, SOURCE, and TARGET.

4. Verify Target Directory Permissions
Ensure that the user specified in REMOTE_USER has write permissions to the target directory on the EC2 instance.

5. Push Changes and Trigger Deployment
Commit any changes made and push them to the repository:
git add .
git commit -m "Update deployment configuration"
git push origin main

The deployment will be triggered automatically based on the configured GitHub Actions workflow.

Workflow Configuration
The deployment workflow (deploy.yml) in the .github/workflows directory specifies the deployment steps using the ssh-deploy action.

For additional customization or troubleshooting, refer to the deployment workflow file.


### .github/workflows/deploy.yml:

```yaml
name: Python Application Deployment

on:
  push:
    branches: [main] # Specify the branch to trigger deployment

jobs:
  deploy:
    name: Deploy on EC2
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Deploy to Server
        uses: easingthemes/ssh-deploy@v2
        with:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          REMOTE_PORT: 22
          SOURCE: "." # Modify source directory if needed
          TARGET: "/var/www/html/"
          ARGS: "-o StrictHostKeyChecking=no"
          # EXCLUDE: "" # Specify excludes if needed

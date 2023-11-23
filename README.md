# Flask_Sample
# Deployment Using GitHub Actions

This repository uses GitHub Actions for automated deployment to an EC2 instance. Follow these steps to set up deployment:

## Step-by-Step Deployment Guide

### 1. Fork or Clone Repository

    Clone or fork this repository to your local machine:
    ```bash
        git clone <repository_url>
        cd <repository_name>


### 2. Configure Secrets in Repository

    Go to your GitHub repository > Settings > Secrets.
    Add the following secrets:
        1. EC2_SSH_KEY: Contents of your SSH private key.
        2. HOST_DNS: Your EC2 instance's DNS or IP address.
        3. USERNAME: Username for SSH access to the EC2 instance.

### 3. Modify GitHub Actions Workflow File

    In .github/workflows/deploy.yml, ensure the following values are correctly set:

    1. SSH_PRIVATE_KEY: Set to ${{ secrets.EC2_SSH_KEY }}.
    2. REMOTE_HOST: Set to ${{ secrets.HOST_DNS }}.
    3. REMOTE_USER: Set to ${{ secrets.USERNAME }}.
    4. Verify other parameters like REMOTE_PORT, SOURCE, and TARGET.

### 4. Verify Target Directory Permissions
    Ensure that the user specified in REMOTE_USER has write permissions to the target directory on the EC2 instance.

### 5. Push Changes and Trigger Deployment
    Commit any changes made and push them to the repository:
    git add .
    git commit -m "Update deployment configuration"
    git push origin main

    The deployment will be triggered automatically based on the configured GitHub Actions workflow.

## Workflow Configuration
    The deployment workflow (deploy.yml) in the .github/workflows directory specifies the deployment steps using the ssh-deploy action.

    For additional customization or troubleshooting, refer to the deployment workflow file.

```bash
name: Python application
    
    on:
      push:
        branches: [ "main", "staging" ]
      pull_request:
        branches: [ "main", "staging" ]
    
    permissions:
      contents: read

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v3
          - name: Set up Python 3.10
            uses: actions/setup-python@v3
            with:
              python-version: "3.10"
          - name: Install dependencies
            run: |
              python -m pip install --upgrade pip
              pip install flake8 pytest
              if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
          - name: Lint with flake8
            run: |
              flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
              flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
          - name: Test with pytest
            run: |
              pytest
    
      deploy:
        name: Deploy on EC2
        runs-on: ubuntu-latest
        needs: build
        steps:
          - name: Checkout the files
            uses: actions/checkout@v2
          - name: Deploy to Server
            uses: easingthemes/ssh-deploy@v2
            with:
              SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
              REMOTE_HOST: ${{ secrets.HOST_DNS }}
              REMOTE_USER: ${{ secrets.USERNAME }}
              REMOTE_PORT: 22
              SOURCE: "."
              TARGET: "/var/www/html/"
              #ARGS: "-o StrictHostKeyChecking=no"
              #EXCLUDE: ""  # You can specify excludes if needed
              

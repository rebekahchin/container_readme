# Docker Readme for Node.js

## Prerequisites
1. Install AWS CLI
   ```bash
   # Uninstall any old versions of AWS CLI
   sudo yum remove awscli

   # Download AWS CLI
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install

   # Check if it installed correctly
   $ aws --version
   aws-cli/2.17.20
   ```
2. Install Docker
   ```bash 
   # install docker
   sudo yum update -y
   sudo yum install docker
   sudo service docker start

   # add ec2-user to docker group so we can run docker commands without sudo
   sudo usermod -a -G docker ec2-user

   # to check if ec2-user can run docker commands without sudo
   docker info

## Base image
0. Note: Use only base images from Redhat to reduce the number of security findings
1. Make an account at [Red Hat](https://access.redhat.com/) > Register
2. Go to [Node.js container image on Red Hat](https://catalog.redhat.com/software/containers/ubi9/nodejs-20/64770ac7a835530172eee6a9)
3. Select "Get this image" > "Using Red Hat login"
4. Using your Red Hat account details, pull the image you want with, replacing the version number with the latest version as on the Red Hat website:
   ```bash
   $ docker login registry.redhat.io
   Username: {REDHAT-USERNAME}
   Password: {REDHAT-PASSWORD}
   Login Succeeded!
   
   $ docker pull registry.redhat.io/ubi9/nodejs-20:<LATEST_VERSION>
   ```

## Building your Node.js Application
1. Make a Dockerfile in the root directory of your Node.js application with: 
   ```
   touch Dockerfile
   ```
2. Use [node.js/Dockerfile](node.js/Dockerfile) as a template
3. Build your Docker image from your Dockerfile with, replacing `<APP-NAME>` with your app's name: 
   ```
   docker build -t <APP-NAME> .
   ```
4. Login to AWS ECR with AWS CLI, replacing `<AWS_ACCOUNT_ID>` with your AWS Account ID: 
   ```
   aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com
   ```
5. Tag your Docker image with, replacing `<APP_NAME>`, `<AWS_ACCOUNT_ID>` and `<REPO-NAME>`: 
   ```
   docker tag <APP-NAME>:latest <AWS_ACCOUNT_ID>.dkr.ecr.region.amazonaws.com/<REPO-NAME>
   ```
6. Push to your ECR repo with, replacing `<AWS_ACCOUNT_ID>` and `<REPO-NAME>`: 
   ```
   docker push <AWS_ACCOUNT_ID>.dkr.ecr.region.amazonaws.com/<REPO-NAME>:latest
   ```
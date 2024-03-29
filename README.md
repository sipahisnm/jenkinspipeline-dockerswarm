# jenkinspipeline-dockerswarm

# Project-205: Jenkins Pipeline for Dockerized Phonebook Application (Python Flask & MySQL) Deployed on Docker Swarm

## Description

This project aims to create a Jenkins pipeline to deploy the Phonebook Application web application with Docker Swarm on Elastic Compute Cloud (EC2) Instances by pulling the app images from the AWS Elastic Container Registry (ECR) repository.

## Problem Statement

![Project_205](Project_205.png)

- Your company has recently started a project that aims to serve as phonebook web application. Your teammates have started to work on the project and developed the UI and backend part of the project and they need your help to deploy the app in development environment.

- You are, as a cloud engineer, requested to create a Jenkins pipeline to deploy the Phonebook Application in the development environment on Docker Swarm on AWS EC2 Instances using AWS Cloudformation Service to showcase the project.

- To prepare the application for deployment, you need to;

  - Create a new public repository for the project on GitHub.

  - Create docker image using the `Dockerfile` from the base image of `python:alpine`.

  - Deploy the app on swarm using `docker compose`. To do so on the `Compose` file;

    - Create a MySQL database service with one replica using the image of `mysql:5.7`;

      - attach a named volume to persist the data of database server.

      - attach `init.sql` file to initialize the database using `configs`.

    - Configure the app service to;

      - pull the image of the app from the AWS ECR repository.

      - deploy the one app for each swarm nodes using `global` mode.

      - run the app on `port 80`.

    - Use a custom network for the services.

- You are also requested; to use AWS ECR as image repository, to create Docker Swarm with 3 manager and 2 worker node instances, to automate the process of Docker Swarm initialization through Cloudformation in the development environment. So, to prepare the infrastructure, you can configure Cloudformation template using the followings;

  - The application stack should be created with new AWS resources.

  - The application should run on Amazon Linux 2 EC2 Instance

  - EC2 Instance type can be configured as `t2.micro`.

  - To use the AWS ECR as image repository;

    - Enable the swarm node instances with IAM Role allowing them to work with ECR repos using the instance profile.

    - Install AWS CLI `Version 2` on swarm node instances to use `aws ecr` commands.

    - Use Amazon ECR Credential Helper to allow Docker to interact with ECR easily.

  - To automate the process of Docker Swarm initialization;

    - Install the docker and docker-compose on all nodes (instances) using the `user-data` bash script.

    - Create a separate instance with `instance profile` to be first manager node of the swarm. Within the `user-data` script;

      - Set the first manager node hostname as `Grand-Master`.

      - Initialize Docker swarm.

      - Create a docker service named `viz` on the manager node on port `8080` using the `dockersamples/visualizer` image, to monitor the swarm nodes easily.

      - Download `docker-compose.yml` file from the repo and deploy application stack on Docker Swarm.

    - Create a launch template with `instance profile` for Manager Nodes. Within the `user-data` script;

      - Install the python `ec2instanceconnectcli` package for `mssh` command.

      - Connect from manager node to the `Grand-Master` to get the `join-token` and join the swarm as manager node using `mssh` command.

    - Create two manager node instances using the `Manager Launch Template`.

    - Create a launch template with `instance profile` for Worker Nodes. Within the `user-data` script;

      - Install the python `ec2instanceconnectcli` package to use `mssh` command.

      - Connect from worker node to the `Grand-Master` to get the `join-token` and join the swarm as worker node using `mssh` command.

    - Create two worker node instance using the `Worker Launch Template`.

  - Create a single security group for all swarm nodes and open necessary ports for the app and swarm services.

  - Tag the swarm node instances appropriately as `Docker Manager/Worker <Number> of <StackName>` to discern them from AWS Management Console.

  - The Web Application should be accessible via web browser from anywhere.

  - Phonebook App Website URL, Visualization App Website URL should be given as output by Cloudformation Service, after the stack created.

- To create a Jenkins Pipelines, you need to launch a Jenkins Server with security group allowing SSH (port 22) and HTTP (ports 80, 8080) connections. For this purpose, you can use pre-configured [*Cloudformation Template for Jenkins Server enabled with Git, Docker, Docker Compose and also configured to work with AWS ECR using IAM role*](./clarusway-jenkins-with-git-docker-ecr-cfn.yml).

- Create a Jenkins Pipeline with following configuration;

  - Create an image repository on ECR for the app.

  - Build the application Docker image and push it to the ECR repository.

  - Build the infrastructure for the app on EC2 instances using Cloudformation template.

  - Deploy the application on Docker Swarm

## Project Skeleton

```text
205-jenkins-pipeline-for-phonebook-app-on-docker-swarm (folder)
|
|----readme.md                  # Given to the students (Definition of the project)
|----phonebook-cfn-template.yml # To be delivered by students (Cloudformation template)
|----Dockerfile                 # To be delivered by students
|----docker-compose.yml         # To be delivered by students
|----init.sql                   # Given to the students (SQL statements to initialize db)
|----jenkins-cfn-template.yml   # Given to the students (Cloudformation template for Jenkins Server)
|----Jenkinsfile                # To be delivered by students
|----app
      |----phonebook-app.py     # Given to the students (Python Flask Web Application)
      |----requirements.txt     # Given to the students (List of Flask Modules/Packages)
      |----templates
            |----index.html      # Given to the students (HTML template)
            |----add-update.html # Given to the students (HTML template)
            |----delete.html     # Given to the students (HTML template)
```

## Expected Outcome

![Phonebook App Search Page](./search-snapshot.png)

### At the end of the project, following topics are to be covered;

- Jenkins Pipeline Configuration

- Docker Swarm Deployment

- Web App and MySQL Database Configuration in Docker Swarm

- Bash scripting

- AWS ECR as Image Repository

- AWS IAM Policy and Role Configuration

- AWS EC2 Launch Template Configuration

- AWS EC2 Configuration

- AWS EC2 Security Group Configuration

- AWS Cloudformation Service

- AWS Cloudformation Template Design

- Git & Github for Version Control System

### At the end of the project, students will be able to;

- demonstrate how to configure Jenkins pipeline to deploy app on Docker Swarm together with Cloudformation Template.

- demonstrate how to configure Dockerfile and docker-compose files.

- set up a Docker Swarm cluster to work with AWS ECR using Cloudformation.

- deploy an application stack on Docker Swarm.

- create and configure AWS ECR from the AWS CLI.

- use Docker commands effectively to tag, push, and pull images to/from ECR.

- demonstrate bash scripting skills using `user data` section in Cloudformation to install and setup environment for Docker Swarm on EC2 Instances.

- demonstrate their configuration skills of AWS EC2, Launch Templates, IAM Policy, Role, Instance Profile, and Security Group.

- configure Cloudformation template to use AWS Resources.

- show how to launch AWS Cloudformation Templates from AWS CLI.

- apply git commands (push, pull, commit, add etc.) and Github as Version Control System.

## Resources

- [AWS Cloudformation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

- [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/index.html)

- [AWS ECR Credential Helper](https://github.com/awslabs/amazon-ecr-credential-helper)

- [Authenticating Amazon ECR Repositories for Docker CLI with Credential Helper](https://aws.amazon.com/blogs/compute/authenticating-amazon-ecr-repositories-for-docker-cli-with-credential-helper/)

- [Docker Compose File Reference](https://docs.docker.com/compose/compose-file/)

- [Docker Reference Page](https://docs.docker.com/reference/)

- [EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Connect-using-EC2-Instance-Connect.html)

- [Jenkins Handbook](https://www.jenkins.io/doc/book/)

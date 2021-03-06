# Udagram Microservices

Udacity Cloud Developer Nanodegree

## Repositories

* [GitHub Repo](https://github.com/ankitlohchab/udacityMicroservices)
* [DockerHub Repo - FrontEnd](https://hub.docker.com/repository/docker/ankitlohchab/udacity-frontend) 
* [DockerHub Repo - Feed](https://hub.docker.com/repository/docker/ankitlohchab/udacity-restapi-feed) 
* [DockerHub Repo - User](https://hub.docker.com/repository/docker/ankitlohchab/udacity-restapi-user) 
* [DockerHub Repo - ReverseProxy](https://hub.docker.com/repository/docker/ankitlohchab/reverseproxy) 

> udacity-frontend repo contain 2 images for `A/B deployment of the application`

## Getting Started

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

### Prerequisites

You need to install:

* [Docker ToolBox](https://docs.docker.com/toolbox/toolbox_install_windows/) else [Docker](https://www.docker.com/products/docker-desktop)
* [AWS CLI](https://aws.amazon.com/cli/)
* [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
* [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

### Installation

Test that your installation was Successful with the following commands:

* `docker --version`
* `aws --version`
* `minikube version`
* `kubectl version --short --client`

![Version](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/Version.png)

## Setup Environment Variables

Copy and Paste the variables with your values:

```
export POSTGRESS_USERNAME=your postgress username;
export POSTGRESS_PASSWORD=your postgress password;
export POSTGRESS_DATABASE=your postgress database;
export POSTGRESS_HOST=your postgress host;
export AWS_REGION=your aws region;
export AWS_PROFILE=your aws profile;
export AWS_BUCKET=your aws bucket name;
export JWT_SECRET=your jwt secret;
```

## Setup Docker Enviroment

Build the images: `docker-compose -f docker-compose-build.yaml build --parallel`

![DockerCompose](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/DockerCompose.png)

List your docker images to check if they have been built: `docker images`

![DockerImages](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/DockerImages.png)

Run your docker containers: `docker-compose up`

![DockerComposeUp](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/DockerComposeUp.png)

To exit run control + C

Push your docker images: `docker-compose -f docker-compose-build.yaml push`

![DockerComposePush](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/DockerComposePush.png)

Check your Docker Hub for the images:

![DockerHubRepositories](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/DockerHubRepositories.png)

### Create a Kubernetes Cluster with MiniKube

In Docker QuickStart Terminal or Windows Powershell use : minikube start

Check the status using: minikube status

![MinikubeConfig](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/MinikubeConfig.png)

## Create Kubernetes Components (Configmaps and Secrets)

Encrypt your database username and password using base64 using the following commands:

* `echo POSTGRESS_PASSWORD | base64`
* `echo POSTGRESS_USERNAME | base64`
Encrypt your aws file using base64 using the following commands:

* `cat ~/.aws/credentials | base64`

Add these values in the appropriate places in your `env-secret.yaml`, `aws-secret.yaml`, and `env-configmap.yaml`.

## Setup Kubernetes Environment

Load secret files:

* `kubectl apply -f aws-secret.yaml`
* `kubectl apply -f env-secret.yaml`
* `kubectl apply -f env-configmap.yaml`
Apply all other yaml files:

* `kubectl apply -f .`

![Secrets](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/Secrets.png)

## Check your Pods Status
Command: `kubectl get all`

![Pod](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/Pod.png)

## Connect the Services with Port Forwarding

Use Port Forwarding to the Frontend and Reverse Proxy services:

> Note: The port forwarding must be done in Separate terminals, to run both services at the same time.

```
kubectl port-forward service/frontend 8100:8100
kubectl port-forward service/reverseproxy 8080:8080
```

## Open your browser

Check: `localhost:8100`

![Application](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/Application.png)


## CI/CD with TravisCI

Sign up for [TravisCI](https://travis-ci.org/) and connect your Github application repository to TrivisCL.
Add `.travis.yml` file to the root of the application.
Copy and paste the following code into your `.travis.yml` file:

```
language: minimal

services: docker

env:
  - DOCKER_COMPOSE_VERSION=1.23.2

before_install:
  - docker -v && docker-compose -v
  - sudo rm /usr/local/bin/docker-compose
  - curl -L https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` > docker-compose
  - chmod +x docker-compose
  - sudo mv docker-compose /usr/local/bin
  - curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
  - chmod +x ./kubectl
  - sudo mv ./kubectl /usr/local/bin/kubectl



install:
  - docker-compose -f udacity-c3-deployment/docker/docker-compose-build.yaml build --parallel 
  
 ```
  
Add your environment variables to the project repository in TravisCI by selecting the setting option.

Commit and Push your changes to trigger a TravisCI build.

> Travis only runs builds on the commits you push after you’ve added a `.travis.yml` file.

Check the build status page to see if your build passes or fails according to the return status of the build command by visiting TravisCI and selecting your repository.

![TravisCI_1](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/TravisCI_1.png)

![TravisCI_2](https://github.com/ankitlohchab/udacityMicroservices/blob/master/screenshots/TravisCI_2.png)


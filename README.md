# Contoso Ship Manager - Demo Application

## Features

This project is a simple harbor control application to track ships. It consists on a front-end and a back-end part.

The application is divided into two main directories: `frontend` and `backend` each one with their respective codes and infrastructure files.

__Front End__: Created using Vim. Just go into the directory, type `npm install` to install all dependencies and then `npm run serve`. You can also build the image from the Dockerfile in the same directory

__Front End__: Created with TypeScript. Just go into the directory, type `npm install` to install all dependencies and then `npm run build:start` to run the app or `npm run start:debug` to start in debug mode (tsc watch and logging). You can also build the image from the Dockerfile in the same directory

## Getting Started

### Prerequisites

- Azure Kubernetes Service Cluster
- Azure CosmosDB (DB type - MongoDB)

### Installation & Quickstart

#### Setup Azure resources

Azure Blueprints enable quick creation of governed subscriptions. This allows Cloud Architects to design environments that comply with organizational standards and best practices – enabling your app teams to get to production faster.

Here we are going to deploy the required Azure resources for this demo using Azure Blueprints.

Below are the resources are created using blueprints

- VNET
- Subnet
- NSG Rules
- Managed System Identity
- Log analytics workspace
- AKS cluster with 2 nodepools
- Cosmos DB for MongoDB API

Use below commands to import the blueprint -
`az login`
`az blueprint import --name sample-project --input-path azure-blueprints`
`az blueprint publish --blueprint-name sample-project --version 0.1`

#### Setup CI and CD processes (option 1 using Jenkins)

__Setup Jenkins Server__

1) Add Helm's stable chart repo:
`helm repo add jenkins https://charts.jenkins.io`
2) Ensure the repo is up to date:
`helm repo update`
3) Run command to run Jenkins in Kubernets Cluster
`helm install cd jenkins/jenkins -f jenkins/values.yaml --version 1.2.2 --wait`
4) Once all pod up and running then setup the Jenkins job using the `Jenkinsfile`

#### Setup CI and CD processes (option 2 using Azure DevOps)

Setup the Azure DevOps pipeline using `azure-pipeline.yaml` file.

This pipeline build the docker images and deploy in AKS cluster.

#### Application hosted image (pre-backed docker image)

There's a hosted image of this application available at the MCR (Microsoft Container Registry) in the following address: `mcr.microsoft.com/mslearn/samples/contoso-ship-manager`

You can pull this image using `docker pull` and run it locally using `docker run`.

There are two tags available for different parts of the image:

- `backend`: Has only the backend of the application.
- `frontend`: Contains the frontend part of the application.

__You'll still need a MongoDB database to run those images__

### Environment Variables

The backend portion requires environment variables to run, those are:

- DATABASE_MONGODB_URI: URI of the MongoDB database
- DATABASE_MONGODB_DBNAME: MongoDB database name

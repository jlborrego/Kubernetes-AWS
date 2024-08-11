# Kubernetes-AWS Deployment

This repository contains the setup and deployment scripts for a Node.js application and MongoDB database on a Kubernetes cluster, utilizing AWS services. 

## Overview

The project includes:

- Building Docker images and deploying them in a Kubernetes cluster.
- Configuring namespaces and contexts.
- Defining and deploying MongoDB and Node.js services.
- Creating PersistentVolumes and PersistentVolumeClaims for data persistence.

## Docker Image and Docker Hub

The Node.js application used in this project is a simple app that manages tasks and stores data using MongoDB. The Docker image for this application is already prepared and published on Docker Hub. You can find the image [here](https://hub.docker.com/r/juanlubh/node-dockercompose).

## Namespace and Context Setup

A namespace `nodejs-unir` has been created to organize resources and achieve better isolation. The following command was used to create the namespace:

```bash
kubectl create namespace nodejs-unir
```

Additionally, a context named unir-context was created, associating this namespace with the cluster and user:
```bash
kubectl config set-context unir-context --namespace=nodejs-unir --cluster=<cluster-name> --user=<user-name>
```

# MongoDB Deployment and Service
The mongodb.yml file defines the deployment of a MongoDB database in the nodejs-unir namespace. This file contains:

## Deployment
- Name: mongodb
- Namespace: nodejs-unir
- Uses the Docker image mongo.
- Exposes port 27017.
- Configures a volume mounted from the host directory /data/mongodb.
  
## Service
- Name: mongo
- Namespace: nodejs-unir
- Exposes port 27017 and directs traffic to the MongoDB pods.
  
# Node.js ReplicaSet and LoadBalancer Service
The nodejs.yml file defines the deployment of a Node.js application with a ReplicaSet and LoadBalancer service:

## Deployment
- Name: nodejs
- Namespace: nodejs-unir
- Configured with 2 replicas.
- Uses the Docker image juanlubh/node-dockercompose:1.0.0.
- Exposes port 3000.
  
## Service
- Name: nodejs-lb
- Namespace: nodejs-unir
- Type: LoadBalancer, which provisions an external load balancer.
- Exposes port 3000 and directs traffic to the Node.js pods.

# Deployment and Verification
To deploy all resources, run:

```bash
kubectl apply -f .
```
After deployment, verify the setup by describing Pods and checking logs:

- MongoDB Pod Information:
```bash
kubectl describe pod <mongodb-pod-name>
```
- Node.js Pod Logs:
```bash
kubectl logs <nodejs-pod-name>
```
You can access the application via the LoadBalancer URL to see the deployed Node.js app.

# PersistentVolume and PersistentVolumeClaim
A separate manifest defines a PersistentVolume and a PersistentVolumeClaim to ensure persistent storage. The mongodb.yml file was updated to use this persistent storage. This setup includes:

## PersistentVolume
- Created with AWS EBS and mounted to /data/mongodb.
## PersistentVolumeClaim
- Claims 5Gi of storage.
This configuration ensures that the MongoDB data is stored on AWS EBS, rather than on the local node storage, providing better persistence and flexibility.

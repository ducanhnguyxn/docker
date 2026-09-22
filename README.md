🐳 Docker & Kubernetes Learning

A hands-on learning repository for understanding Docker and Kubernetes, starting from the fundamentals and gradually moving toward containerized applications, networking, volumes, Docker Compose, Kubernetes deployments, services, and scaling.

The goal of this repository is to learn how modern applications are built, packaged, deployed, and managed using containers.

⸻

📚 What I’m Learning

🐳 Docker

* What containers are
* Docker vs Virtual Machines
* Docker images and containers
* Dockerfiles
* Docker commands
* Port mapping
* Container networking
* Environment variables
* Volumes and persistent data
* Docker Hub
* Docker Compose
* Multi-container applications
* Containerizing Node.js applications
* Containerizing databases

☸️ Kubernetes

* Kubernetes fundamentals
* Kubernetes architecture
* Pods
* Deployments
* ReplicaSets
* Services
* ConfigMaps
* Secrets
* Namespaces
* Kubernetes networking
* Scaling applications
* Rolling updates
* Persistent volumes
* Kubernetes configuration files
* Basic application deployment

⸻

🐳 Docker

What is Docker?

Docker is a platform that allows applications to run inside containers.

A container packages an application together with the dependencies it needs to run.

Instead of saying:

“It works on my computer.”

Docker helps make the environment more consistent:

Application
    +
Dependencies
    +
Runtime
    ↓
Docker Container

⸻

Docker vs Virtual Machines

Virtual Machine

Computer
│
├── Host OS
│
├── Virtual Machine
│   ├── Guest OS
│   └── Application
│
└── Virtual Machine
    ├── Guest OS
    └── Application

Docker

Computer
│
├── Host OS
│
├── Docker
│
├── Container
│   └── Application
│
└── Container
    └── Application

Containers usually require fewer resources because they share the host operating system’s kernel instead of running a complete guest OS.

⸻

📦 Important Docker Concepts

Image

A Docker image is a blueprint used to create containers.

For example:

Node.js Image
      ↓
Docker Container
      ↓
Node.js Application

Container

A container is a running instance of an image.

docker run node

This creates and runs a container using the Node.js image.

⸻

🔨 Dockerfile

A Dockerfile contains instructions for building a Docker image.

Example:

FROM node:22
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]

Explanation

FROM node:22

Uses Node.js as the base image.

WORKDIR /app

Sets /app as the working directory.

COPY package*.json ./

Copies the package files into the container.

RUN npm install

Installs the application dependencies.

COPY . .

Copies the rest of the application.

EXPOSE 3000

Documents that the application uses port 3000.

CMD ["npm", "start"]

Starts the application when the container runs.

⸻

🚀 Basic Docker Commands

Check Docker version

docker --version

Download an image

docker pull nginx

List images

docker images

Run a container

docker run nginx

Run in the background

docker run -d nginx

Map a port

docker run -p 8080:80 nginx

Now:

localhost:8080
      ↓
Container port 80

List running containers

docker ps

List all containers

docker ps -a

Stop a container

docker stop <container-id>

Remove a container

docker rm <container-id>

Remove an image

docker rmi <image-id>

View container logs

docker logs <container-id>

Open a shell inside a container

docker exec -it <container-id> bash

⸻

🏗️ Building My Own Image

Build an image from a Dockerfile:

docker build -t my-app .

Run it:

docker run -p 3000:3000 my-app

The general workflow is:

Application
     ↓
Dockerfile
     ↓
docker build
     ↓
Docker Image
     ↓
docker run
     ↓
Docker Container

⸻

💾 Docker Volumes

Containers are generally treated as disposable.

If a container is deleted, data stored only inside that container can disappear.

Volumes allow data to persist.

docker volume create my-data

Example:

docker run -v my-data:/app/data my-app

The data can survive even if the container is removed.

⸻

🌐 Docker Networking

Containers can communicate with each other through Docker networks.

Create a network:

docker network create my-network

Run containers on the network:

docker run -d --name backend --network my-network my-backend
docker run -d --name database --network my-network mongo

Now the backend can communicate with the database using its container name:

backend → database

⸻

🧩 Docker Compose

Docker Compose allows multiple containers to be defined and managed together.

Example:

services:
  backend:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - database
  database:
    image: mongo
    ports:
      - "27017:27017"

Start the application:

docker compose up

Run in the background:

docker compose up -d

Stop everything:

docker compose down

A typical application might look like:

                Docker Compose
                     │
        ┌────────────┴────────────┐
        │                         │
     Backend                   Database
     Node.js                    MongoDB
        │                         │
        └───────────┬─────────────┘
                    │
                Application

⸻

☸️ Kubernetes

What is Kubernetes?

Kubernetes is a platform for managing containers at scale.

Docker is mainly concerned with creating and running containers.

Kubernetes helps manage many containers across a cluster.

A simplified idea:

Docker
   ↓
Run containers
Kubernetes
   ↓
Manage containers
   ↓
Scale
   ↓
Restart failed containers
   ↓
Distribute workloads
   ↓
Update applications

⸻

🏗️ Kubernetes Architecture

A Kubernetes cluster contains:

Kubernetes Cluster
│
├── Control Plane
│
└── Worker Nodes
    │
    ├── Pod
    │   └── Container
    │
    ├── Pod
    │   └── Container
    │
    └── Pod
        └── Container

⸻

📦 Pod

A Pod is the smallest deployable unit in Kubernetes.

Most commonly, a Pod contains one container:

Pod
└── Container
    └── Node.js App

Kubernetes does not normally manage individual containers directly. It manages Pods.

⸻

🚀 Deployment

A Deployment tells Kubernetes how an application should run.

Example:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: my-backend:latest
          ports:
            - containerPort: 3000

The important part:

replicas: 3

means Kubernetes should maintain three Pods.

Deployment
    │
    ├── Pod 1
    ├── Pod 2
    └── Pod 3

If one Pod fails, Kubernetes can create another one.

⸻

🌐 Kubernetes Service

Pods can be temporary and their IP addresses can change.

A Service provides a stable way to access a group of Pods.

Example:

apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 3000

The structure becomes:

                 Service
                    │
          ┌─────────┼─────────┐
          ↓         ↓         ↓
        Pod 1     Pod 2     Pod 3
          │         │         │
       Backend   Backend   Backend

⸻

📈 Scaling

Kubernetes can increase the number of application replicas.

kubectl scale deployment backend --replicas=5

Now:

Deployment
│
├── Pod 1
├── Pod 2
├── Pod 3
├── Pod 4
└── Pod 5

⸻

🔄 Rolling Updates

Kubernetes can update applications gradually instead of stopping everything at once.

For example:

Version 1
│
├── Pod 1
├── Pod 2
└── Pod 3
        ↓ Update
Version 2
│
├── Pod 1 → v2
├── Pod 2 → v2
└── Pod 3 → v2

This helps reduce downtime during deployments.

⸻

🔐 ConfigMaps & Secrets

Applications often need configuration such as:

DATABASE_URL
PORT
API_URL
JWT_SECRET

Kubernetes provides:

ConfigMap

For non-sensitive configuration.

Secret

For sensitive values such as passwords, API keys, and tokens.

Example:

env:
  - name: DATABASE_URL
    valueFrom:
      secretKeyRef:
        name: database-secret
        key: url

⸻

🛠️ Basic Kubernetes Commands

Check Kubernetes:

kubectl version

View Pods:

kubectl get pods

View Deployments:

kubectl get deployments

View Services:

kubectl get services

Create resources:

kubectl apply -f deployment.yaml

Delete resources:

kubectl delete -f deployment.yaml

View Pod logs:

kubectl logs <pod-name>

Describe a Pod:

kubectl describe pod <pod-name>

⸻

🧠 Docker vs Kubernetes

Docker	Kubernetes
Builds container images	Manages containerized applications
Runs containers	Manages Pods
Dockerfile	Kubernetes YAML
Docker Compose	Kubernetes cluster
Good for local development	Designed for orchestration
Manages individual containers	Manages applications across clusters

They are not really competitors.

A common workflow is:

Write Application
       ↓
Create Dockerfile
       ↓
Build Docker Image
       ↓
Push Image to Registry
       ↓
Kubernetes pulls Image
       ↓
Create Pods
       ↓
Expose with Service
       ↓
Scale / Update / Monitor

⸻

🧪 Learning Projects

Project 1: Dockerize a Node.js App

Build a simple Express API and run it inside Docker.

Topics:

* Dockerfile
* Images
* Containers
* Ports
* Docker commands

⸻

Project 2: Node.js + MongoDB

Create:

Node.js API
     │
     ↓
MongoDB

Run both using Docker Compose.

Topics:

* Multiple containers
* Networks
* Volumes
* Environment variables
* Docker Compose

⸻

Project 3: Deploy Node.js to Kubernetes

Take the previous application and deploy it to Kubernetes.

Topics:

* Pods
* Deployments
* Services
* Kubernetes YAML
* Scaling

⸻

Project 4: Kubernetes Full Stack

Build:

                Kubernetes
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
     Frontend      Backend     Database
      React        Node.js      MongoDB
        │            │            │
        └────────────┴────────────┘

Learn:

* Multiple Deployments
* Services
* ConfigMaps
* Secrets
* Networking
* Persistent storage
* Scaling

⸻

🗺️ Learning Roadmap

Docker Basics
     ↓
Docker Images
     ↓
Dockerfiles
     ↓
Containers
     ↓
Networking
     ↓
Volumes
     ↓
Docker Compose
     ↓
Containerized Node.js App
     ↓
        Kubernetes
           ↓
        Pods
           ↓
     Deployments
           ↓
       Services
           ↓
    ConfigMaps & Secrets
           ↓
        Scaling
           ↓
   Rolling Deployments
           ↓
 Persistent Storage
           ↓
    Kubernetes Projects
           ↓
      Cloud / AWS

⸻

🎯 Goal

The main goal of this repository is not just to memorize Docker or Kubernetes commands.

I want to understand why they exist, how they work, and how they fit together in real-world software development.

By the end of this learning journey, I should be able to:

* Build Docker images
* Run and manage containers
* Write Dockerfiles
* Use Docker Compose
* Containerize backend applications
* Connect applications to databases
* Understand Kubernetes architecture
* Create Kubernetes Deployments
* Create Services
* Scale applications
* Manage application configuration
* Deploy containerized applications to Kubernetes

⸻

📝 Notes

This repository is a personal learning environment. Commands, examples, experiments, and projects will be added as I learn more about Docker, Kubernetes, containerization, and cloud deployment.

Learn it → build it → break it → fix it → understand it. 🐳☸️
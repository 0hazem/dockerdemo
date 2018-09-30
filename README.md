# Containerize a Ruby on Rails Application on Kubernetes ![CI status](https://img.shields.io/badge/build-passing-brightgreen.svg)

Build and deploy a Rails application that uses PostgreSQL, Redis and Sidekiq on Kubernetes

### Requirements
* A functioning Kubernetes Cluster or MiniKube
* Kubectl

## Setup Instructions
### 1. Cloning and setting up the repository
```
$ git clone https://github.com/0hazem/rails-kubernetes.git
$ cd rails-kubernetes
```
### 2. Build the Docker Image
```
$ docker build -t drkiq:v1 .
```
### 3. Edit Environment Variables
* Environment Variables are passed to containers in the Yaml deployment configs. 

### 3. Prepare Persistent Volumes for PostgreSQL and Redis
```
$ kubectl create -f drkiq-postgress-persistentvolume.yaml
$ kubectl create -f drkiq-redis-persistentvolume.yaml
```
### 4. Create a job to initialize the database
```
$ kubectl create -f db-migrate.yaml
```
### 5. Create the deployments and corresponding services
```
$ kubectl create -f postgress-deployment.yaml
$ kubectl create -f redis-deployment.yaml
$ kubectl create -f drkiq-deployment.yaml
$ kubectl create -f sidekiq-deployment.yaml
```
### 6. Accessing the app
* The application is accessible using a NodePort service
```
# Use this command to get the external IP of the master
$ kubectl cluster-info

# Use this commmand to get the external port assigned to the drkiq nodeport service
$ kubectl get service

# use MasterIPURL:NodePort to access the application page
```
* Live edits in the drkiq container are reflected immediately

## Using Docker Compose
Alternatively Docker Compose can be used to manage the containers instead of Kubernetes. In that case use the included docker-compose.yml file and the following commands: 
```
#Store your environment variables in a .drkiq.env file

# Create Volumes for postgreSql and Redis
$ docker volume create --name drkiq-postgres
$ docker volume create --name drkiq-redis

# Initialize the database
$ docker­-compose run --­­user "$(id ­-u):$(id -­g)" drkiq rake db:reset
$ docker­-compose run --­­user "$(id ­-u):$(id -­g)" drkiq rake db:migrate

# Start up the containers stack
$ docker-compose up
```

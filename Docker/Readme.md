# **DOCKER**   
<p align="center">
   <img width="539" alt="image" src="https://github.com/AquibMS/DevOps/assets/83283216/87d92153-dc6a-498d-b1f4-f51060b82efc">   
</p>
Docker is a very popular and powerful open source containerization platform that is used for billing, deploying and running apps.   
It's main purpose is to decouple apps/software from the underlying infrastructure.   

#### What is Container?   
A unit of software bundled with dependencies which helps apps to be deployed fast and reliably between different computing platforms.   
   
In simpler terms it can be assumed docker as a big ship carrying huge boxes of products(containers) just to deploy to production with minimal config changes.   
   
Docker container doesn't require the installation of OS. It just relies on the usage of Kernel's resources and its functionality to allocate them for the CPU and memory.   
   
Containers can be deployed on multiple platforms like bare instances, virtual machines, Kubernetes platform etc as per requirements basis of scale or desired platform.   
   
The main aim of docker containers is to get rid of the infrastructure dependency while deploying and running apps, which means any containerized app can run on any platform irrespective of the infra being used behind the scenes.   
   
In technical terms, they are just run time instances of **DOCKER IMAGES**.   
   
#### What are Docker Images?   
Docker images are executable packages bundled with application code & dependencies, software packages etc for the purpose of creating containers. These images can be deployed to any docker environment and the containers can be spun up there to run the application.   
   
In order to create a docker images a list of commands has to be executed which are present in a **DOCKER FILE**.   
<p align="center">
   <img width="548" alt="image" src="https://github.com/AquibMS/DevOps/assets/83283216/10ddfa04-9f1d-4647-8c76-8486786789a3">
</p>   

#### Docker Compose   
Docker Compose is a tool provided by Docker that allows us to define and run multi-container Docker applications. It uses a YAML file to configure the services, networks, and volumes required for the application.   
With Docker compose file, we can define the configuration of the application's services such as databases, web servers and other dependencies in a single **docker-compose.yml** file.   
##### 1. Services   
Services in Docker Compose represent the individual components of the application, such as web servers, databases, or other microservices. Each service is typically defined by specifying the Docker image to use, along with any additional configuration options.   
###### Example:   
```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
```

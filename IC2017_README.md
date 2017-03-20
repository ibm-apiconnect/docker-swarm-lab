# Docker Swarm and API Connect - Hands-on Lab

Microservices are revolutionizing how applications are developed and deployed. The microservice architecture augmented by containerization technology provided by Docker allows for flexibility, rapid iteration, high availability and more. The days of monolithic applications are coming to an end; in this lab you'll learn about container technology, and the integration of IBM API Connect with Docker Swarm to manage and deploy microservices.

## Prepare your workspace

To stay organized, let's create a workspace and clone this repository:
```
mkdir ~/workspace
cd ~/workspace
git clone http://github.com/ibm-apiconnect/docker-swarm-lab.git
```

## Open a Terminal

Every time you open a terminal, you'll need to run the following to switch to sudo user:

```
sudo su -
# password is passw0rd
cd ~/workspace
```

## Exercises

Run through each of the exercises sequentially - they should only take 5-10 min each.

* Exercise 1: [Initialize Docker Swarm and Add Nodes to the Cluster](ex1/README.md)
* Exercise 2: [Deploy a Private Registry Service to the Swarm](ex2/README.md)
* Exercise 3: [Create an API Microservice](ex3/README.md)
* Exercise 4: [Dockerize your API microservice](ex4/README.md)
* Exercise 5: [Deploy your API Microservice to Docker Swarm](ex5/README.md)
* Exercise 6: [Load-balance your API Microservices with NGINX](ex6/README.md)

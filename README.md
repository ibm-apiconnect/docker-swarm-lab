# Docker Swarm and API Connect - Hands-on Lab

Microservices are revolutionizing how applications are developed and deployed. The microservice architecture augmented by containerization technology provided by Docker allows for flexibility, rapid iteration, high availability and more. The days of monolithic applications are coming to an end; in this lab you'll learn about container technology, and the integration of IBM API Connect with Docker Swarm to manage and deploy microservices.

## Prerequisites

1. Setup atleast two networked host machines, preferably running Linux.
1. Install Node.js and API Connect Toolkit `npm i -g apiconnect`
1. Install Docker Engine 1.12 or newer
1. Ensure the following ports are open:
   * TCP port 2377 for cluster management communications
   * TCP and UDP port 7946 for communication among nodes
   * UDP port 4789 for overlay network traffic
1. Clone this GitHub repository to one of the machines - this will be your "manager" machine.

## Exercises

Run through each of the exercises sequentially - they should only take 5-10 min each.

* Exercise 1: [Initialize Docker Swarm and Add Nodes to the Cluster](ex1/README.md)
* Exercise 2: [Deploy a Private Registry Service to the Swarm](ex2/README.md)
* Exercise 3: [Create an API Microservice](ex3/README.md)
* Exercise 4: [Dockerize your API microservice](ex4/README.md)
* Exercise 5: [Deploy your API Microservice to Docker Swarm](ex5/README.md)
* Exercise 6: [TBD]()
* Exercise 7: [TBD]()

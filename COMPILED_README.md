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

# Exercise 1 - Initialize Docker Swarm and Add Nodes to the Cluster

In this exercise, you'll initialize Docker Swarm. Docker Swarm provides clustering and orchestration capabilities to allow you to turn a group of machines running Docker into a single, virtual Docker engine.

## Initialize Swarm Manager Node

1. Open a terminal in the same VM that you cloned this GitHub repository
1. Identify the IP address of your VM. In Linux, you can can open the Connection Information Prompt on the top right and find your address under the `IPv4` section. You can also use `ifconfig` to find the IP. It should look something like `192.168.x.x`.
1. Run the following command:
```
docker swarm init --advertise-addr <IP-from-previous-step>
```
The IP specified by the `--advertise-addr` parameter allows worker nodes to find and join the swarm created by the master node.

You should see the following output:
```
localuser@local-vm:~$ docker swarm init --advertise-addr 192.168.231.130
Swarm initialized: current node (abk00th1nhe3lb4fvjyiqvitf) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-477by6an1fjqbwrnyz1evf4j29p3lvsziv1j6fy3pc5gi7iz4a-1tx4v5ij76aachfukvhl55q9u \
    192.168.231.130:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Copy the `docker swarm join` command for the next step.

## Add Swarm Worker Nodes

To demonstrate Docker Swarm service model and networking, you'll need to add some worker nodes to the swarm.

Run the command from the previous step in each additional machine you want to join the swarm. Ensure that each machine is able to access IP defined by your `--advertise-addr`.

You should see the following output:
```
$ docker swarm join \
  --token SWMTKN-1-1e4qkzhijskumj17epwoms5oa8uy7tmejvcvskqarsvcxvlvhh-ayutk7rk8plzesr97wryfjvs9 \
  192.168.231.140:2377

This node joined a swarm as a worker.
```

To ensure that your swarm nodes have been properly added, switch to your manager node and run `docker node ls`:
```
ID                           HOSTNAME                   STATUS  AVAILABILITY  MANAGER STATUS
ovd9qwjf95eo3x5ijdi2ecjzg    localuser-virtual-machine  Ready   Active        
yyuuufleycap2uvmgovszem18 *  master                     Ready   Active        Leader
```
The asterisk indicates your current machine. All nodes should be `Ready` Status and `Active`.

## Next Steps

In the next exercise, you'll [deploy your first service to the swarm](../ex2/README.md).

# Deploy a Private Registry Service to the Swarm

In this exercise, you will deploy a private Docker registry to swarm. This will allow us to build custom applications as Docker images and push them to a registry where they can be pulled by every VM within the swarm.

## Docker Registry image

Docker Hub is essentially a package manager but for Docker images. It's powered by the Docker `registry` image which is conveniently one of the images you can pull from Docker Hub.

## Create a Service on Swarm

On the manager node, run the following command to create your own locally running Docker registry service:

```
docker service create -p 5000:5000 --name registry registry:2
```

By default, this command will create one instance of the service. You'll keep it at exactly one instance as if you scale it up, the registry instances can get out of sync with each other.

## Check that the Registry is Running

Run the following command to see every service running on the swarm:
```
docker service ls
```

You should see the following:
```
ID            NAME          MODE        REPLICAS  IMAGE
nlqmvnomfk1g  registry      replicated  1/1       registry:2
```

## Next Steps

Now that you have a registry, you can start publishing images to it. This in-turn allows Docker Swarm to deploy them as services across all of your VMs. With this infrastructure in place, you'll need to create some microservices and "dockerize" them.

In the next exercise, you will [build an API application using the API Connect open-source LoopBack framework](../ex3/README.md).

# Create an API Microservice

In this exercise, you will create a set of APIs using LoopBack, and open-source Node.js framework for creating applications. These APIs will allow us to perform CRUD (create, read, update and delete) operations on database entries.

## Create a Mongo Database

To perform CRUD operations, you'll need first need to create and start a DB. On your master VM, run the following command:
```
docker run --name my-mongo -p 27017:27017 -d mongo
```
That's it! You've created a MongoDB that you can now use to perform CRUD operations. This database is accessible on port 27107 at the same IP that you identified in [Exercise 1](../ex1/README.md). In the next step, we'll create an application that will allow you to work with your newly created database.

## Scaffold a sample LoopBack Application

Ensure you're in your workspace directory and create an empty folder for your application code.
```
cd ~/workspace
mkdir sample-lb
cd sample-lb
```

Run `apic loopback`. When prompted, choose the "empty-server" option:
```
$ apic loopback

? What's the name of your application? sample-lb
? What kind of application do you have in mind? empty-server (An empty LoopBack API, without any configured models or datasources)

...clipped...

Next steps:

  Change directory to your app
    $ cd /Users/svennam/IC2017/sample-lb

  Create a model in your app
    $ apic create --type model

  Compose your API, run, manage, enforce and deploy it with API Connect
    $ apic edit

  Run the app
    $ apic start
```

You'll see that it scaffolds your application by creating a Node.js project with code ready to go. It then automatically runs `npm install` to fetch the dependencies your application needs to run.

## Launch the API Connect toolkit

API Connect comes with a toolkit that allows you to visually create, test and deploy APIs. To launch it, simply run `apic edit` from the directory of your application.

The first time you open it, it may ask you to log-in. If you have a Bluemix account, you can use it to log-in. Otherwise, make an account on [Bluemix](https://console.ng.bluemix.net/registration).
```
apic edit
```

## Configure your DB connection

Click the Data Sources tab along the top and click the `Add` button. Name your DB `mongo` and press `New`. In the next window, under `Connector` choose `MongoDB`. It will prompt you to install the connector - follow the prompts to install the connector. You'll only need to enter two more fields - the `Host` and `Port`. For `Host`, enter the same IP from [Exercise 1](../ex1/README.md). For `Port`, use 27017. Hit save - this will test the connection and should come back successful.

## Configure a Model

Next, we'll need to define the type of object we want to store in the Database. Hit the `All Datasources` button to go back. Then jump to the Models tab and `Add` a new model. You can get creative here with what type of object you want to store, but here is an example model I've created to represent a simple car:

<img src="SS1.png"  width="800">

Note: Under the `Data Source` option, ensure that you've chosen the DB connection you created in the previous step, `mongo`. 

## Explore your APIs

After launching the toolkit, you can start your API application on your machine and start testing the APIs. To do so, press the play button on the bottom of your window - this starts the LoopBack application along with an API gateway. Wait for it to start - it will say `Running`. This should take less than 30 seconds. Next, hit the Explore button on the top right which launches a Swagger-based view (Open API Spec) of the APIs that are available.

Along the left side, you should see a number of operations with the model that you created in the previous step. Let's try calling a series of these operations.

#### GET /<Model>

Let's test retrieving the list of objects for the Model you created earlier.

Navigate to the operation `GET /<Model>`. Along the right side, there is a black section which shows you how to call that operation, provides boiler code, and has a button "Call Operation". Hit the button to call your GET operation.

<img src="SS2.png"  width="800">

You might get a CORS error. Override the CORS error by clicking the link, adding the exception, and then closing the tab. Then retry the `Call Operation`.

You should see a `200 OK` response but an empty array - this is because we haven't added any objects to the DB yet.

#### POST /<Model>

Let's test adding an object to the database.

Navigate to the operation `POST /<Model>` to create a database entry. Scroll down to the "Call Operation" button. Right above that button, there is a field to enter parameters. Hit `Generate` then customize the automatically generated data. Then hit the `Call Operation` button to POST your object to the Mongo database.

You should see a `200 OK` response, as well as a response body indicating that the database update has succeeded. 

To test that the objects really are in the database, you can call `GET /<Model>` again to retrieve the stored objects.

## Next steps
Now that you've created an API microservice, you'll need to dockerize it before you deploy it to the swarm. In the next step, you'll [create a Docker image from the application you just created](../ex4/README.md).

# Dockerize your API microservice

In this exercise, you'll create a Docker image from your Node.js application and deploy it to the Swarm.

## The Dockerfile

Dockerfiles allow you to describe the process of building an image. In this directory, there is a Dockerfile that allows you to build Docker images for Node.js applications.

Let's quickly walk through what each step of the Dockerfile does:

### Dockerfile
```
# A starting point - comes with Debian and a number of development header packages
FROM node:6

# Create app directory for the application inside the Docker image
RUN mkdir -p /usr/src/app

# Change the working directory to the application directory
WORKDIR /usr/src/app

# Start by copying over package.json to install app dependencies
COPY package.json /usr/src/app/
# Run `npm install` to install all deps
RUN npm install

# Copy over application code
COPY . /usr/src/app

# Define 3000 as port to be exposed.
EXPOSE 3000

# Use this command to start application in the Docker container
CMD [ "npm", "start" ]
```

### .dockerignore
```
node_modules
```
The `.dockerignore` has this single line entry and tells Docker to avoid copying over local copy of the `node_modules` as we are building them in the Dockerfile.

Jump back to your terminal. If `apic edit` is still running, run `CTRL+C` to close the toolkit as we no longer need it. Copy over the `Dockerfile` and `.dockerignore` files into the LoopBack application directory.

```
cp ~/workspace/docker-swarm-lab/ex4/* .
```

## Build the Docker image

To build a docker image by executing the instructions in the `Dockerfile`, you'll run `docker build --tag localhost:5000/sample-lb .`. Note the ` .` at the end which tells Docker to build from the current directory. The `--tag` tells Docker that it will be part of your private regsitry.

To check that your image is built and tagged, run the following command and verify the output:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
localhost:5000/sample-lb      latest              04b44316b6af        2 weeks ago         30.8 MB
node                          6                   cde8ba396275        2 weeks ago         659 MB
registry                      2                   d1e32b95d8e8        6 weeks ago         33.2 MB
```

## Push the Docker image to your Private registry

Next, push your docker image to the registry you deployed in [Exercise 2](../ex2/README.md).

```
$ docker push localhost:5000/sample-lb
```

## Next steps

Now that you have you API microservice available in the registry as a Docker image, swarm is able to deploy it as a service to each node in the swarm. However, before you do that you'll need to create an overlay network for each of the microservices. This will allow you to use a gateway to secure access to your application as well as provide load balancing - this will be covered in more detail in a later exercise. [Jump to the next exercise](../ex5/README.md).

# Deploy your API Microservice to Docker Swarm

In the previous exercise, you created a Node.js LoopBack application, dockerized it and pushed it to your Docker registry. Now, Docker Swarm will be able to deploy it as a service to each node in the swarm. The first thing you need is an overlay network that will host each instance of your microservice and the API gateway that you will deploy in a later exercise.

## Create an overlay network

Run the following command:
```
docker network create --driver overlay mynet
```

That's it!

## Deploy your API Microservice to Docker Swarm

Now that you've created the overlay network, create a service on the swarm with the image you pushed to the registry in [Exercise 4](../ex4/README.md).

```
docker service create --network mynet --name sample-lb --replicas 3 localhost:5000/sample-lb
```

This deploys your microservice to the swarm under the "mynet" network you created above. It names it sample-lb for easy identification. The `--replicas` tag tells it to deploy it as 3 instances spread across the VMs. Finally, the image name `localhost:5000/sample-lb` tells swarm where to pull the image from when it tries to start it on each of the VMs.

## Verifying and Inspecting your services

To see every service running in your swarm, you can run the following:

```
$ docker service ls
ID            NAME          MODE        REPLICAS  IMAGE
k9zdxtu3ar01  sample-lb     replicated  3/3       localhost:5000/sample-lb:latest
nlqmvnomfk1g  registry      replicated  3/1       registry:2
```

To see more information about a service and see where every node is running, you can run the following:

```
docker service ps sample-lb
```

## Next steps

You may have noticed that you cannot directly access these microservices as a container port was not published. In a distributed network like this, you'll want to have a single endpoint that handles API access and load balancing. In the next exercise, [you'll deploy an NGINX gateway that allows access to and load-balances your API endpoints](../ex6/README.md).

# Load-balance your API Microservices with NGINX

In the previous exercise, you deployed your Node.js LoopBack application to the Swarm and scaled it up to 3 replicas. Now you'll deploy an NGINX load balancer to allow access to those APIs.

For the next part, there are two options for creating your NGINX container. You can simply copy over the files from this Git repository or you can create the files yourself.

## Copy over NGINX configuration files (OPTION 1)
```
cd ~/workspace
mkdir nginx
cd nginx
cp ~/workspace/docker-swarm-lab/ex6/* .
```

Continue below at [Build your NGINX Docker Container](#build-your-nginx-docker-container)

## Manually create NGINX configuration files (OPTION 2)
### Create an NGINX configuration file

Run the following commands to create a new file that will become your NGINX conf file:
```
cd ~/workspace
mkdir nginx
cd nginx
touch default.conf
```

Add the following to that configuration file :
```
server {
  listen 80;
  location / {
    proxy_pass http://backend;
  }
}

upstream backend {
  server sample-lb:3000;
}
```

### Create a Dockerfile for your NGINX Docker container
Next, we'll need to build our NGINX docker container. Create a `Dockerfile`:

```
touch Dockerfile
```

Add the following to that file:
```
FROM nginx
COPY default.conf /etc/nginx/conf.d/default.conf
```

## Build your NGINX Docker container
Run the following to build and run your NGINX Docker container:
```
docker build -t localhost:5000/custom-nginx .
docker push localhost:5000/custom-nginx
docker service create --name my-nginx -p 8080:80 --network mynet localhost:5000/custom-nginx
```

## Access your APIs!

Now that you've got a gateway in front of your APIs, you can start accessing them! In your browser, hit the following URL to call `GET` on your APIs:

```
localhost:8080/api/<Model>
```

## Conclusion

In this series of exercises, you've done the following:
* Created a API Microservice using API Connect that performs CRUD operations against data in a MongoDB,
* Scaled the microservice to multiple instances using Docker Swarm, and
* Deployed an NGINX gateway to load-balance access to your API microservice

API Connect powered by LoopBack provides an quick and easy method for creating Node.js microservices for performing CRUD operations against a datastore. You also used Docker Swarm, a powerful tool that allows you to create production-ready applications and services with ease. The infrastructure we've setup today can be re-used and extended for much more complex applications and enterprise level applications.

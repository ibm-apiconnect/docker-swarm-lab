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
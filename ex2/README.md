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

# Exercise 1 - Initialize Docker Swarm and Add Nodes to the Cluster

In this exercise, you'll initialize Docker Swarm. Docker Swarm provides clustering and orchestration capabilities to allow you to turn a group of machines running Docker into a single, virtual Docker engine.

## Initialize Swarm Manager Node

1. Open a terminal in the same VM that you cloned this GitHub repository
1. Identify the IP address of your VM. In Linux, you can can open the Connection Information Prompt on the top right and find your address under the `IPv4` section.
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

To demonstrate Docker Swarm service model and networking, we'll need to add some worker nodes to the swarm.

Run the command from the previous step in each additional machine you want to join the swarm. Ensure that each machine is able to access IP defined by your `--advertise-addr`.

You should see the following output:
```
This node joined a swarm as a worker.
```

To ensure that your swarm nodes have been properly added, switch to your manager node and run `docker node ls`:
```
ID                           HOSTNAME                   STATUS  AVAILABILITY  MANAGER STATUS
4z0i1x83m7nwjy3qol8jy6cc6    worker-node-1  Ready   Active        
abk00th1nhe3lb4fvjyiqvitf *  manager-node  Ready   Active        Leader
```
The asterisk indicates your current machine. All nodes should be `Ready` Status and `Active`.

## Next Steps

In the next exercise, we'll [deploy our first service to the swarm](../ex2/README.md).

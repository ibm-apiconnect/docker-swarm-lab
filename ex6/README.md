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

# Dockerize your API microservice

In this exercise, you'll create a Docker image from your Node.js application and deploy it to the Swarm.

## The Dockerfile

Dockerfiles allow you to describe the process of building an image. In this directory, there is a Dockerfile that allows you to build Docker images for Node.js applications.

Let's quickly walk through what each step of the Dockerfile does:

### Dockerfile:
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

### .dockerignore:
```
node_modules
```

Copy over the `Dockerfile` and `.dockerignore` files into the application directory. The `.dockerignore` file has one entry - `node_modules`. This tells Docker to rebuild the node_modules every time instead of copying them over.

## Build the Docker image

To build a docker image by executing the instructions in the `Dockerfile`, you'll run `docker build --tag localhost:5000/sample-lb `. The tag tells Docker that it will be part of your private regsitry.

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

Now that you have you API microservice available in the registry as a Docker image, swarm is able to deploy it as a service to each node in the swarm. However, before you do that you'll need to create an overlay network for each of the microservices. This will allow you to secure connections to each microservice as well as use an API gateway to load balance and enforce policies - this will be covered in more detail in a later exercise. [Jump to the next exercise](../ex5/README.md).

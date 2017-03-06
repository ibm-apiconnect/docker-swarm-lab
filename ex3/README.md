# Create an API Microservice

In this exercise, you will create a set of APIs using LoopBack, and open-source Node.js framework for creating applications. These APIs will allow us to perform CRUD (create, read, update and delete) operations on database entries.

## Scaffold a sample LoopBack Application

Create an empty folder for your application code.

```
mkdir sample-lb
cd sample-lb
```

Run `apic loopback`. When prompted, choose the "notes" sample:
```
$ apic loopback

? What's the name of your application? sample-lb
? What kind of application do you have in mind? notes (A project containing a basic working example, including a memory database)

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

The first time you open it, it may ask you to log-in. If you have a Bluemix account, you can use it to log-in. Otherwise, you can set the use the `SKIP_LOGIN=true` option to launch apic edit:
```
SKIP_LOGIN=true apic edit
```

## Customize your API (optional??)
### TODO : STEPS TO CUSTOMIZE API

## Explore your APIs

After launching the toolkit, you can start your API application on your machine and start testing the APIs. To do so, press the play button on the bottom of your window - this starts the LoopBack application along with an API gateway. Next, hit the Explore button on the top right which launches a Swagger-based view (Open API Spec) of the APIs that are available.

### TODO: walk user through get/post/get some sample API.

## Next steps
Now that you've created an API microservice, you'll need to dockerize it before you deploy it to the swarm. In the next step, you'll [create a Docker image from the application you just created](../ex4/README.md).

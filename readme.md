# Simple Python web application

This repo is for building a simple Python web application running on Docker Compose. The application uses the Flask framework and maintains a hit counter in Redis. While the sample uses Python, the concepts demonstrated here should be understandable even if you’re not familiar with it.

## Table of Contents
  - [Usage](#Usage)
    - [Prerequisites](#Prerequisites)
    - [Run Instructions](#Run-Instructions)
    - [Handling transient errors](#Handling-transient-errors)
  - [Build Info](#Build Info)  
    - [STEPS (HIGH LEVEL)](#STEPS-(HIGH-LEVEL))


# Usage 


## Prerequisites

Make sure you have already installed both Docker Engine and Docker Compose. You don’t need to install Python or Redis, as both are provided by Docker images.


# Run Instructions

From your project directory, start up your application by running 

```
docker-compose up.
```

Compose pulls a Redis image, builds an image for your code, and starts the services you defined. In this case, the code is statically copied into the image at build time.

Enter http://0.0.0.0:5000/ (or http://localhost:5000) in a browser to see the application running.

If you’re using Docker Machine on a Mac or Windows, use docker-machine ip MACHINE_VM to get the IP address of your Docker host. Then, open http://MACHINE_VM_IP:5000 in a browser.

your images should be available by running docker image ls 

You can inspect images with docker inspect <tag or id>.

Stop the application, either by running docker-compose down from within your project directory in the second terminal, or by hitting CTRL+C in the original terminal where you started the app.



## Redis 

In this example, redis is the hostname of the redis container on the application’s network. We use the default port for Redis, 6379.


## Handling transient errors

Note the way the get_hit_count function is written. This basic retry loop lets us attempt our request multiple times if the redis service is not available. This is useful at startup while the application comes online, but also makes our application more resilient if the Redis service needs to be restarted anytime during the app’s lifetime. In a cluster, this also helps handling momentary connection drops between nodes.

# Build Info

## STEPS (HIGH LEVEL)

1. Created an app called app.py
2. Created requirements file with (flask and redis declared)
3. Created a Dockerfile
4. Define service in a compose file (docker-compose.yml)
5. Build and run
6. Add bind mount
7. Rebuild and add more functionality


# DOCKERFILE 

Build an image starting with the Python 3.4 image.
Add the current directory . into the path /code in the image.
Set the working directory to /code.
Install the Python dependencies.
Set the default command for the container to python app.py.

# COMPOSE FILE 

This Compose file defines two services, web and redis. The web service:

Uses an image that’s built from the Dockerfile in the current directory.
Forwards the exposed port 5000 on the container to port 5000 on the host machine. We use the default port for the Flask web server, 5000.
The redis service uses a public Redis image pulled from the Docker Hub registry.


# Edit the Compose file to add a bind mount

Edit docker-compose.yml in your project directory to add a bind mount for the web service:

```

version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: "redis:alpine"
```

The new volumes key 

```
    volumes:
     - .:/code
```


mounts the project directory (current directory) on the host to /code inside the container, allowing you to modify the code on the fly, without having to rebuild the image.

Shared folders, volumes, and bind mounts

If your project is outside of the Users directory (cd ~), then you need to share the drive or location of the Dockerfile and volume you are using. If you get runtime errors indicating an application file is not found, a volume mount is denied, or a service cannot start, try enabling file or drive sharing. Volume mounting requires shared drives for projects that live outside of C:\Users (Windows) or /Users (Mac), and is required for any project on Docker for Windows that uses Linux containers. For more information, see Shared Drives on Docker for Windows, File sharing on Docker for Mac, and the general examples on how to Manage data in containers.

If you are using Oracle VirtualBox on an older Windows OS, you might encounter an issue with shared folders as described in this VB trouble ticket. Newer Windows systems meet the requirements for Docker for Windows and do not need VirtualBox.


# Command Summary 

## See What's running 
docker-compose ps

```
Adams-MacBook-Pro:simplePython adammcmurchie$ docker-compose ps


        Name                      Command               State           Ports         
--------------------------------------------------------------------------------------
simplepython_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp              
simplepython_web_1     python app.py                    Up      0.0.0.0:5000->5000/tcp
```

## Run Silent 

```
docker-compose up -d
```
If you started Compose with docker-compose up -d, stop your services once you’ve finished with them:
```
$ docker-compose stop
```

## One off Commands 

The docker-compose run command allows you to run one-off commands for your services. For example, to see what environment variables are available to the web service:

```
$ docker-compose run web env

```
## Shutdown 

```
docker-compose down
```
## Fully tear down 

You can bring everything down, removing the containers entirely, with the down command. Pass --volumes to also remove the data volume used by the Redis container:
```
$ docker-compose down --volumes
```
 
At this point, you have seen the basics of how Compose works.
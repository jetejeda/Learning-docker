# Docker Overview

While working on a project we may have an application stack that includes different technologies. Each technology has its own Libraries and Dependencies. This often leads to compatibility issues with the underlying OS because we have to make sure each technology is compatible with the version of OS we were planning to use. Secondly, we had to check the compatibility between these services and the libraries and dependencies on the OS.

Every time there is a new developer on the team, they had to go through a large set of instructions and run hundreds of commands to finally set up their environment. Typically, we also have different environments (dev, test, prod, etc) therefore, we couldn't guarantee that the application that we were building would run the same way in different environments.

Docker comes in to play solve those issues. With docker you can create an architecture were each service/technology runs independently (in separate containers) with its own Dependencies and Libraries. All the containers can run on the same VM and the OS but withing separate environments or containers, we just had tu build the Docker configuration once. Thanks to this, developers can get started with a single docker run command.

## ¿What are containers?

Containers are completely isolated environments. As in they can have their own processes, services, network interfaces, their own mounts, just like VM's except they all share the same OS Kernel.

Docker utilizes LXC containers. Containers are meant to run a specific task or process, once it's completed, the container exits. A container only lives as long as the process inside it is alive.

## Containers vs. Virtual Machines (VM's)

Docker is installed above the OS, meaning that docker is installed on the OS. Docker has less isolation as more resources are shared between the containers.
![alt text](./img/container-architecture.png)

With VM's, we have the hypervisor on the hardware and then the VM's on them. Each VM has its own OS inside it. This causes a higher utilization of underlying resources as there are multiple virtual OS and Kernel running. The VM's also consume higher disk space. Thanks to this, Docker containers boot up faster. They are completely isolated from each other.
![alt text](./img/vm-architecture.png)

The common approach is to have containers and VM's. When you have large environments with thousands of application containers running on thousands of docker hosts you will often see containers provisioned on virtual docker hosts

## Docker Images

An image is a package/template that is used to create one or more containers. Containers are running instances of images that are isolated and have their own environments and set up processes.

# Docker commands

## docker run

Is used to run a container from an image. syntax:

```sh
docker run imageName
```

If the image is not present on the host, it will go out to docker hub and pull the image down.

## docker ps

Is used to list all running containers and basic information about them. Each container automatically gets a random ID and name, it's created by Docker.

To see all containers regardless if they are running or not, use the -a flag

```sh
docker ps -a
```

## docker stop

To stop a running container. You must provide the container id or the container name.

```sh
docker stop containerNameOrID
```

## docker rm

Used when we want to get rid of a container. After running this command, if you see the container name, the command succeeded. You can remove more than one container with the same commands, you just have to separate each container with a blank space

```sh
docker rm containerName1 containerNID2
```

## docker images

See a list of all available images and their sizes on our host.

```sh
docker images
```

## docker rmi

Use this to remove an image. You must ensure that no containers are running off of the image before attempting to remove it. You must stop and delete all dependent containers to be able to delete an image.

```sh
docker rmi imageName
```

## docker pull

Use this to only pull an image and not run a container.

```sh
docker pull imageName
```

## docker exec

Use this command to execute a command on your running docker container.

```sh
docker exec containerName your-command
docker exec ubuntu-container cat /etc/hosts
```

## run attach/detach

When you run a container with the docker run containerName command, it will run in the foreground (attach mode) meaning you will be attached to the console or the standard out of the docker container and you will see the output of the specific service on your screen. You won't be able to do anything else on that console other than view the output until the docker container stops.

You can run a container in a detach mode. This will run the docker container in the background mode and you will be back to your prompt immediately.

```sh
docker run -d imageName
```

If you would like to attach back to the running detach container, run the docker attach command and specify the name or ID of the docker container:

```sh
docker attach containerNameOrID
```

If you're specifying the ID of a container in any Docker command, you can simply provide the first few characters alone just so it is different from the other containers ID.

# Docker run

As you may noticed when we use a docker run command, we only specified the image that we will use for that container, but we can also specify the version of the image that we want to use. By default, docker will use the image that has the tag "latest". We can indicate the specific tag that we want using the next syntax:

```sh
docker run yourImage:image.version
docker run ubuntu:4.04
```

Everything after the ":" is the . Docker will pull the image that has that tag. An image can have multiple tags related to it.

## Inputs

When we have an application that needs an input. If we use docker to run that application, it wouldn't for the input prompt. It will only perform the standard output. That is because by default, docker containers does not listen to a standard input, even though you are attached to its console. It doesn't have a terminal to read inputs from (runs in a non interactive mode). If you'd like to provide your input, you must map the standard input of your host to the Docker container using the next syntax:

```sh
docker run -i imageName
```

That parameter stands for interactive mode.

Expected behavior:
![alt text](./img/params-expected.png)

Current result:
![alt text](./img/params-current.png)

Although we have provided the -i option, we are missing the "Welcome! Please enter your name: " prompt. That is because the application is prompted on the terminal, but we haven't attached to the containers terminal. In order to solve this, we can send the command with the -it option.

```sh
docker run -it imageName
```

![alt text](./img/params-detached.png)

## Port mapping or port publishing

The underlying host where Docker is installed is called Docker host (or Docker engine). When we run a containerized application, we are able to see that its running and we are able to access to that application with the next two options.

1. Use the IP of the Docker container: Every Docker container gets an IP assign by default. This is an internal IP that is only accessible within the Docker host.
2. Use the IP of the Docker host: For this to work, we must have mapped the port inside the Docker container to a free port on the host

```sh
docker run -p 80:5000 imageName
```

In the previous example, my users will have access to my application through Port 80 on my Docker host. As mentioned, my container is running on the port 5000 from the Docker container IP. All the traffic on port 80 on the docker host will get routed to port 5000 inside the Docker container.

This way, you can run multiple instances of your application and map them to different ports on the docker host or run instances of different applications on different ports. You cannot map to the port of the docker host more than once. You cannot add a port mapping while the service/container is running, first you have to stop it.
![alt text](./img/port-mapping.png)

## Volume mapping

This is used to persist data in a Docker container. Docker containers have their own isolated file system. Any changes to any files happen within the container.

What happens if you were to delete a container and remove it? As soon as you do that, the container, along with all the data inside it gets blown away (all the data is gone). If you would like to persist data, you would want to map a directory outside the container on the docker host to a directory inside the container. This way, when the Docker container runs, it will implicitly mount the external directory to a folder inside the container. Therefore, all the data will be stored in the external volume and it will remain even if you delete the docker container.

```sh
docker run -v docker/host/folder:container/folder imageName
docker run -v /opt/datadir:/var/lib/mysql mysql
```

### Show the details of a container

It returns all details of a container in JSON format.

```sh
docker inspect containerNameOrID
```

## Container Logs

Useful to see the logs of container we run in the background. Logs are the contents written to the standard out of a container.

```sh
docker logs containerNameOrID
```

## Sending a command while running a container

```sh
docker run imageName your-command
docker run ubuntu cat /etc/*release*
```

# Docker images

With docker you can create your own images based on your own requirements. The first thing is to understand what application we are creating an image for and how its built.

The common steps are:

1. Create a Dockerfile
2. Install an OS (Ubuntu)
3. Update apt repo
4. Install dependencies using the apt command
5. Install software (python) dependencies using pip
6. Copy the source code of the application to a location (it will be copied to a folder within the container)
7. Define an ENTRYPOINT and run the server/code

Build the docker image

```sh
docker build . -f Docerfile -t yourImageName
```

This will create an image locally on your system. In order to make it available on the Public Docker Hub Registry, run the push command with the image tag we have just created. In order to publish to the docker hub, first you need to be logged in to your docker account.

You can only push to repositories under your own account.

```sh
docker login
#Type your username and password
docker push accountRepo/yourImageName
```

## Dockerfile

Is a text file written in an specific format that docker can understand. It follow the "instruction argument" format. Every docker image must be based off of another image, either an OS or another image that was created.

```Dockerfile
FROM Ubuntu

RUN apt-get update && apt-get -y install python python-pip
RUN pip install flask flask-mysql
COPY . /opt/source-code
ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```

| INSTRUCTION | ARGUMENT                                    | USE                                                                     |
| :---------- | :------------------------------------------ | :---------------------------------------------------------------------- |
| FROM        | Ubuntu                                      | All docker files must start with this instruction                       |
| RUN         | apt-get update && apt-get -y install python | Run a particular command on the base images                             |
| COPY        | . /opt/source-code                          | Copy a file from the local system onto the image                        |
| ENTRYPOINT  | FLASK_APP=/opt/source-code/app.py flask run | Specify a command that will be run when the image is run as a container |

When a docker image is built. Each line of instruction creates a new layer in the Docker image with just the changes from the previous
layer. All the layers built are cached by Docker. Thanks to this, in case a particular step was to fail, it will reuse the previous layers from cache and continue to build the remaining layers. The same is true if you were to add additional steps in the Docker file. You can see the size for each step using the history command:

```sh
docker history yourImageName
```

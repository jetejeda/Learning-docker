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
docker run -d containerName
```

If you would like to attach back to the running detach container, run the docker attach command and specify the name or ID of the docker container:

```sh
docker attach containerNameOrID
```

If you're specifying the ID of a container in any Docker command, you can simply provide the first few characters alone just so it is different from the other containers ID.

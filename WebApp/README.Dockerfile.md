# Dockfile & Docker commands

> **NOTES**: This document is an extension content for document [Containerise Web API application](./README.md). It is advisable to go through it before reading this document.

&nbsp;

## Inspecting the Dockfile

The Dockerfile tells Docker what you want to put into the image and the steps to do that. The objective is place the compiled app and its dependencies into the image. That image becomes a deloyable object.

Referring to the [Dockerfile](./Dockerfile), the actions in the file can be summarised below.

1. Start off with dotnet sdk 6.0 image as the base image.
2. Copy the source code from the local environment to image.
3. Build and publish the application in the image.
4. Switch to dotnet aspnet 6.0 image.
5. Copy the compiled app from step 3 to new directory.
6. Create an user with lesser privilege and assign it to the `USER`. The `USER` is the default user to run the application.
7. Define where to start when launching the container.

&nbsp;

> **TIPS**:
> 
> 1. The `COPY` instruction does not allow path '../' which navigates up in directory trees. Please be mindful on placing the Dockfile so that all files could be copied into image.
> 2. There are times we need to install other tools in the image to facilitiate the development, for instance, cURL. Dockerfile is the right place to do that.
> 3. Avoid placing credential information in the Dockerfile.

&nbsp;

## Use `docker build` & `docker run` on Dockerfile

The `docker compose` command simplifies a lot of works for us. In some scenarios, you might need to use `build` and `run` instead of `compose`. This section explains how to achieve that.

The goal is to build an image and run a container based on that image.

To build an image from a Dockerfile, run command

```
docker build -t <image name and optional tag> -f <dockerfile> .
```

We have the below in our example.

```
docker build -t webapp-server:latest -f Dockerfile .
```

> **TIPS**: the `.` at the end of the command is required. It provides the docker the directory of the build context.

To verify the image build output, run command `docker image ls` to list the images

```
REPOSITORY                                 TAG         IMAGE ID       CREATED          SIZE
webapp-server                              latest      81735a5bc462   47 minutes ago   107MB
```

&nbsp;
Next, we run `docker run` to run a container from the image above.

```
docker run -it --rm -p 8080:80 --name webapp-server-1PS webapp-server
```

`-it` tells docker to stay with the container's putty interactively after the launch
`--rm` tells docker to remove the container after the application exists in container
`-p 8080:80` tells docker to forward request at local port 8080 to container port 80
`--name webapp-server-1PS` assign container this name
`webapp-server` tells docker to run a container from this image.

Similarly, run `docker container ls` to verify the container.

```
CONTAINER ID   IMAGE           COMMAND               CREATED         STATUS         PORTS                  NAMES
c541556c8134   webapp-server   "dotnet DevOps.dll"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->80/tcp   webapp-server-1PS
```

We could also run `docker inspect <container name>` to have detailed look on the container.

The command `docker compose` has done all above for us. 

&nbsp;

## Verify the Web API app in Container

In [Containerise Web API application](./README.md)'s step 4, we issue curl command in local env to verify if the contanerised app is running fine.

There are times, for instance, troubleshooting, we need to check the service in container directly, leaving the network layer outside the investigation scope.

To do that, we use `docker exec`. This command allows us to run a program in container outside container.

```
docker exec -it webapp-server-1PS sh
```

Command above executes program shell in container `webapp-server-1PS` and stay interactive with it with the option `-it`.

Like the curl command in local environment, we issue the command `curl http://localhost:80/weatherforecast` to verify it.

Note the port here, it is `80` instead of `8080`. The containerised app is listening at port 80 inside the container.

> TIPS:
> The image have to have cURL program installed for issuing the command curl in the container. In order to do that, we need to put the install script with instruction `RUN` in the Dockerfile.



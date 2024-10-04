# Containerise Web API Application

The section below explains how to containerise a web api application in local environment. The database is not part of the containerisation journey.

&nbsp;

## STEP 0: Create weatherforecast web api app

As a start, we create a simple default dotnet webapi application using command

```
webapp> dotnet new webapi
```

&nbsp;

## STEP 1: Verify the app runs OK in local dev environment

We have to make sure the web api is running OK before containerisation. Run command below to ensure it builds successfully.

```
webapp> dotnet run
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:7111
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5256
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /Users/p1/Documents/dev/DevOps/WebApp/
```

Run curl command to the lisening port and make sure it runs OK

```
webapp> curl https://localhost:7111/weatherforecast
[{"date":"2024-09-27T11:04:27.696441+08:00","temperatureC":49,"temperatureF":120,"summary":"Cool"},{"date":"2024-09-28T11:04:27.696451+08:00","temperatureC":51,"temperatureF":123,"summary":"Scorching"},{"date":"2024-09-29T11:04:27.696451+08:00","temperatureC":-19,"temperatureF":-2,"summary":"Cool"},{"date":"2024-09-30T11:04:27.696452+08:00","temperatureC":14,"temperatureF":57,"summary":"Cool"},{"date":"2024-10-01T11:04:27.696452+08:00","temperatureC":22,"temperatureF":71,"summary":"Freezing"}]
```

> **TIPS**: Use only single tool/IDE here, VSCODE, with mutliple terminals saves some efforts by not needing to switch to other tools. There is no need to use a separate browser.

`Ctrl+C` to kill the running application.

&nbsp;

## STEP 2: Get the Docker Desktop

Certainly, we need download and install Docker Desktop. Launch Docker Desktop to have Docker daemon running.

To verify if Docker daemon is running, run command

```
docker 
Usage:  docker [OPTIONS] COMMAND

A self-sufficient runtime for containers
docker...
```

&nbsp;

## STEP 3: Create the Dockerfile

Navigate to WebApp directory.

Similar to git command, run `docker init`. Answer the questions and the output are like below.

```
webapp> docker init

Welcome to the Docker Init CLI!

This utility will walk you through creating the following files with sensible defaults for your project:
  - .dockerignore
  - Dockerfile
  - compose.yaml
  - README.Docker.md

Let's get started!

? What application platform does your project use? ASP.NET Core
? What's the name of your solution's main project? DevOps
? What version of .NET do you want to use? 6.0
? What port do you want to use to access your app? 8080

✔ Created → .dockerignore
✔ Created → Dockerfile
✔ Created → compose.yaml
✔ Created → README.Docker.md

→ Your Docker files are ready!
  Review your Docker files and tailor them to your application.
  Consult README.Docker.md for information about using the generated files.

What's next?
  Start your application by running → docker compose up --build
  Your application will be available at http://localhost:8080
```

Let's inspect the files before proceed further.

```
- DevOps
  - WebApp
    - compose.yaml
    - Dockerfile
    - .dockerignore
    - ...
  - ...
```

**Dockerfile** is the definition of the image to be built. Every single image requires a dockerfile.

**compose.yaml** is configuration file that comes handy when there are multiple containers to build and run at the same time. In a simple use case like this, we could use `docker build`and `docker run` to achieve the same result.

**.dockerignore** is similar to .gitignore. It specifies files that you want to exclude in the build.

&nbsp;

## STEP 4: Run the container

Run command `docker compose up --build` and you will get the below.

```
webapp> docker compose up --build
...
 ✔ Network webapp_default     Created                                                0.2s
 ✔ Container webapp-server-1  Created                                                0.1s
Attaching to server-1
server-1  | info: Microsoft.Hosting.Lifetime[14]
server-1  |       Now listening on: http://[::]:80
server-1  | info: Microsoft.Hosting.Lifetime[0]
server-1  |       Application started. Press Ctrl+C to shut down.
server-1  | info: Microsoft.Hosting.Lifetime[0]
server-1  |       Hosting environment: Production
server-1  | info: Microsoft.Hosting.Lifetime[0]
server-1  |       Content root path: /app/
```

The line `Now listening on: http://[::]:80` say the app is running. It accepting request from all IPs and listening to port 80 in the container.

> TIPS: The [::] above is important. With this configuration, it accepts request outside the container. If you find localhost instead of [::], the service can only accept request within container.

To verify if the app is running successfully, we have to check few things. The `docker compose up --build` achieve a number of things behind the scene for us. It first build an image and run a container based on that image.

To check the available images, run `docker image ls`

```
webapp> docker image ls
REPOSITORY                                 TAG         IMAGE ID       CREATED          SIZE
webapp-server                              latest      81735a5bc462   47 minutes ago   107MB
```

The webapp-server is the image built with the command `docker compose`.

We further check the available containers, run command `docker container ls`

```
webapp> docker container ls
CONTAINER ID   IMAGE           COMMAND               CREATED         STATUS         PORTS                  NAMES
c541556c8134   webapp-server   "dotnet DevOps.dll"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->80/tcp   webapp-server-1PS 
```

There is one container with image webapp-server running.

Lastly, we verify the service of the containerised app outside container, i.e. in the local dev environment.

Note the `PORTS` column in the command `docker container ls`. It means the request to port 8080 in local dev environment is forwarded to port `80` in the container. Run command `curl http:localhost:8080/weatherforecast` and it responds the below.

```
webapp> curl http://localhost:8080/weatherforecast
StatusCode        : 200
StatusDescription : OK
Content           : [{"date":"2024-09-27T07:26:50.9071123+00:00","temperatureC":0,"temperatureF":32,"summary":"Balmy"},{ 
                    "date":"2024-09-28T07:26:50.9071248+00:00","temperatureC":-17,"temperatureF":2,"summary":"Chilly"},{ 
                    ...
```

The web api app has been successfully containerised and consumable in local dev environment.

&nbsp;

## STEP 5: Clean Up

Command `docker compose up --build` build and run the container in step 5. Now, run command `docker compose down` to tear down the container.

```
webapp> docker compose down 
[+] Running 2/2
 ✔ Container webapp-server-1  Removed                                                                               0.5s 
 ✔ Network webapp_default     Removed
```

Run command `docker container ls` again to verify it.

```
webapp> docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

The container has been removed. The image, however, is not being removed.

&nbsp;

## Further Reading

1. [Dockerfile & Docker commands](./README.Dockerfile.md)
2. [compose.yaml & `docker compose`](./README.Compose.yaml)


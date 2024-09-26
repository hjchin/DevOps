# Containerise Web API Application

The section below explains how to containerise a web api application in local environment. The database is not part of the containerisation journey.

&nbsp;

## Step 0: Create weatherforecast web api app

As a start, we create a simple default dotnet webapi application using command

```
dotnet new webapi
```

&nbsp;

## Step 1: Verify the app runs OK in local dev environment

We have to make sure the web api is running OK before containerisation. Run cli command below to ensure it builds successfully.

```
dotnet run
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
      Content root path: /Users/p1334906/Documents/dev/DevOps/WebApp/
```



Run curl command to the lisening port and make sure it runs OK

```
curl https://localhost:7111/weatherforecast
[{"date":"2024-09-27T11:04:27.696441+08:00","temperatureC":49,"temperatureF":120,"summary":"Cool"},{"date":"2024-09-28T11:04:27.696451+08:00","temperatureC":51,"temperatureF":123,"summary":"Scorching"},{"date":"2024-09-29T11:04:27.696451+08:00","temperatureC":-19,"temperatureF":-2,"summary":"Cool"},{"date":"2024-09-30T11:04:27.696452+08:00","temperatureC":14,"temperatureF":57,"summary":"Cool"},{"date":"2024-10-01T11:04:27.696452+08:00","temperatureC":22,"temperatureF":71,"summary":"Freezing"}]
```

> **TIP**: Use only single tool/IDE here, VSCODE, with mutliple terminals saves some efforts by not switching to other tools. There is no need to use a separate browser.

&nbsp;

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

Similar to git command, run `docker init`. Answer the questions and the output are like below.

```
docker init

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







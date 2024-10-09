# Develop with Container

In previous section, we started with containerization on an existing web app and ended with deployment to Azure App Service.

In this document, we explain how to develop web app with container. Imagine that you have a new member just onboarded to the team. Typically, the new member has to install all dependencies and tools before the work could start. He has to install not only the dependencies with same version but also the tools with same version to get started. It could take up 1 day or 2 or even longer.

Apart from ensuring the same dependencies in development and production environment, container helps developer to get the development environment ready so much easier compared to the old way. New member of the team could kick start development in seconds. 

It is all possible to develop app in container because of 2 key reaons:
- Container encapsulates the dependencies in it 
- Tool such as `docker compose watch` auto builds the app when there is change in the source files.

Let's get started.

&nbsp;

> **NOTES**: 
> - Docker Desktop is a paid product if the licence conditions are met. You may find alternatives **[Docker Engine](https://docs.docker.com/engine/)** if it suits your needs.
> - VSCODE has an extension for developing app in container, [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers).

&nbsp;

## STEP 0:

Launch Docker Desktop.

&nbsp;

## STEP 1: Add Develop Specification to compose.yaml

Open compose.yaml and add below to the file.

```
...
    develop:
      watch:
        - path: .
          action: rebuild
          target: /source
...
```

The above tells docker to watch files in `.` path, copy it to `target` when changes are detected and execute `rebuild` action.

> **TIPS**: You may defines the file not to be watched in the .dockerignore.

&nbsp;

## STEP 2: Run the Web App with `docker compose watch`

launch a terminal and navigate to WebApp dicetory. Run command below

```
WebApp> docker compose watch
[+] Building 7.0s (18/18) FINISHED                                                                        docker:desktop-linux
...
[+] Running 2/2
 ✔ Network webapp_default     Created                                                                                     0.1s 
 ✔ Container webapp-server-1  Started                                                                                     0.5s 
Watch enabled
```

Based on the log, the app is running and it is being watched.

We first verify if the running service is OK. Hit the URL `http://localhost:8080/weatherforecast` on browser. The response carries only 1 object. The app is running production environment. We are expecting only 1 object in the response.

&nbsp;

## STEP 3: Make changes to the app

Next, we modify appsettings.json to trigger the watch. Change the value of `ResultCount` to 30. It means the service returns 30 objects.

```
...
  "ResultCount": 30
...
```

Once the file is saved, we notice the following in the terminal.

```
Rebuilding service "server" after changes were detected...
[+] Building 1.2s (17/17) FINISHED                                                                        docker:desktop-linux
...
service "server" successfully built
```

> **NOTES**: If you have turn on autosave in IDE, the build will be whenever the file is autosaved by IDE.

&nbsp;

Refresh `http://localhost:8080/weatherforecast` on browser and we will see 30 objects in the response.

```
[
  {
    "date": "2024-10-10T06:59:58.481893+00:00",
    "temperatureC": 18,
    "temperatureF": 64,
    "summary": "Freezing"
  },
  {
    "date": "2024-10-11T06:59:58.4825441+00:00",
    "temperatureC": 42,
    "temperatureF": 107,
    "summary": "Warm"
  },
  ...
   {
    "date": "2024-11-06T06:59:58.4825503+00:00",
    "temperatureC": -16,
    "temperatureF": 4,
    "summary": "Warm"
  },
  {
    "date": "2024-11-07T06:59:58.4825504+00:00",
    "temperatureC": 47,
    "temperatureF": 116,
    "summary": "Freezing"
  },
  {
    "date": "2024-11-08T06:59:58.4825506+00:00",
    "temperatureC": 25,
    "temperatureF": 76,
    "summary": "Chilly"
  }
]
```

Lastly, run command `docker compose down` to tear down everything and release resources.

&nbsp;

## Some thoughts on this approach

- **PROS**:
  
  - It evelates the team member onboarding process to the project. New team member does not need to spend days to get the development ready for the first task.
  - All project team members use identical dependencies. There is no room for errors which could be costly when the error only to be found out in higher environments.

&nbsp;

- **CONS**:
  
  - Developer needs to learn container technologies. It further blurring the boundary between application developer and operation developer.
  - It is tool dependent. Docker Desktop is a paid product in some conditions. Some might need to find alternatives if the cost is an issue. 

&nbsp;

## References

- [Docker Compose Specification](https://docs.docker.com/reference/compose-file/develop/)


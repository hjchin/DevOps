# Develop with container

In previous section, we started with containerization on an existing web app and ended with deployment to Azure App Service.

In this document, we explain how to develop web app with container. Imagine that you have a new member just onboarded to the team. Typically, the new member has to install all dependencies and tools before the work could start. He not only has to install the same version of the dependencies and sometimes same version for the tools as well to get started. It could take up 1 day or 2 or even longer.

Apart from ensuring the same dependencies in development and production environment, container helps developer to get the development environment ready so much easier compared to the old way. New member of the team could kick start development in seconds. It is all possible because container encapsulates the dependencies in it and tool `docker compose watch` auto build the app when there is change in the source files.

Let's get started.

> **NOTES**: Docker Desktop is paid product if the licence conditions are met. You may find alternatives **[Docker Engine](https://docs.docker.com/engine/)** if it suits your needs.

&nbsp;

## STEP 0:

Launch Docker Desktop.

&nbsp;

## STEP 1: Add Develop Specification to compose.yaml

Open compose.xml and add below to the file.

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

We verify if the running service is OK. Hit the URL `http://localhost:8080/weatherforecast` on browser. The response carries only 1 object. The app is running production environment. One object in the response is expected.

&nbsp;

## STEP 3: Make changes to the app

Next, we modify appsettings.json to trigger the watch. Change the value of `ResultCount` to 30. We should expect the service returns 30 objects.

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

> **NOTES**: If you have turn on autosave in IDE, the build will be triggered very often.

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
  {
    "date": "2024-10-12T06:59:58.4825468+00:00",
    "temperatureC": -9,
    "temperatureF": 16,
    "summary": "Scorching"
  },
  {
    "date": "2024-10-13T06:59:58.482547+00:00",
    "temperatureC": 34,
    "temperatureF": 93,
    "summary": "Freezing"
  },
  {
    "date": "2024-10-14T06:59:58.4825471+00:00",
    "temperatureC": -20,
    "temperatureF": -3,
    "summary": "Sweltering"
  },
  {
    "date": "2024-10-15T06:59:58.4825476+00:00",
    "temperatureC": 12,
    "temperatureF": 53,
    "summary": "Freezing"
  },
  {
    "date": "2024-10-16T06:59:58.4825479+00:00",
    "temperatureC": 35,
    "temperatureF": 94,
    "summary": "Freezing"
  },
  {
    "date": "2024-10-17T06:59:58.482548+00:00",
    "temperatureC": 0,
    "temperatureF": 32,
    "summary": "Warm"
  },
  {
    "date": "2024-10-18T06:59:58.4825481+00:00",
    "temperatureC": 12,
    "temperatureF": 53,
    "summary": "Bracing"
  },
  {
    "date": "2024-10-19T06:59:58.4825483+00:00",
    "temperatureC": 9,
    "temperatureF": 48,
    "summary": "Freezing"
  },
  {
    "date": "2024-10-20T06:59:58.4825484+00:00",
    "temperatureC": 43,
    "temperatureF": 109,
    "summary": "Sweltering"
  },
  {
    "date": "2024-10-21T06:59:58.4825485+00:00",
    "temperatureC": 43,
    "temperatureF": 109,
    "summary": "Balmy"
  },
  {
    "date": "2024-10-22T06:59:58.4825486+00:00",
    "temperatureC": 29,
    "temperatureF": 84,
    "summary": "Cool"
  },
  {
    "date": "2024-10-23T06:59:58.4825487+00:00",
    "temperatureC": -5,
    "temperatureF": 24,
    "summary": "Scorching"
  },
  {
    "date": "2024-10-24T06:59:58.4825488+00:00",
    "temperatureC": -6,
    "temperatureF": 22,
    "summary": "Warm"
  },
  {
    "date": "2024-10-25T06:59:58.4825489+00:00",
    "temperatureC": 25,
    "temperatureF": 76,
    "summary": "Balmy"
  },
  {
    "date": "2024-10-26T06:59:58.4825491+00:00",
    "temperatureC": -1,
    "temperatureF": 31,
    "summary": "Warm"
  },
  {
    "date": "2024-10-27T06:59:58.4825492+00:00",
    "temperatureC": 38,
    "temperatureF": 100,
    "summary": "Hot"
  },
  {
    "date": "2024-10-28T06:59:58.4825493+00:00",
    "temperatureC": -11,
    "temperatureF": 13,
    "summary": "Cool"
  },
  {
    "date": "2024-10-29T06:59:58.4825494+00:00",
    "temperatureC": -20,
    "temperatureF": -3,
    "summary": "Warm"
  },
  {
    "date": "2024-10-30T06:59:58.4825495+00:00",
    "temperatureC": 48,
    "temperatureF": 118,
    "summary": "Cool"
  },
  {
    "date": "2024-10-31T06:59:58.4825496+00:00",
    "temperatureC": 8,
    "temperatureF": 46,
    "summary": "Sweltering"
  },
  {
    "date": "2024-11-01T06:59:58.4825497+00:00",
    "temperatureC": -12,
    "temperatureF": 11,
    "summary": "Chilly"
  },
  {
    "date": "2024-11-02T06:59:58.4825498+00:00",
    "temperatureC": 27,
    "temperatureF": 80,
    "summary": "Balmy"
  },
  {
    "date": "2024-11-03T06:59:58.48255+00:00",
    "temperatureC": 51,
    "temperatureF": 123,
    "summary": "Sweltering"
  },
  {
    "date": "2024-11-04T06:59:58.4825501+00:00",
    "temperatureC": 26,
    "temperatureF": 78,
    "summary": "Sweltering"
  },
  {
    "date": "2024-11-05T06:59:58.4825502+00:00",
    "temperatureC": 20,
    "temperatureF": 67,
    "summary": "Balmy"
  },
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
  
  - Developer needs to learn container technologies, further blurring the boundary of application developer and operation developer.
  - It is tool dependent. Docker Desktop is a paid product in some conditions. Some might need to find alternatives if the cost is an issue. 

&nbsp;

## Links for Further Reading

- [Docker Compose Specification](https://docs.docker.com/reference/compose-file/develop/)


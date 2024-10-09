# Promote Web App from Staging to Production

This document explains the application environment promotion. We use App Service feature, Deployment Slot, to promote the app from Staging to Production. It allows developer to swap the content of app service. It could be useful in scenario whereby app review could be done in staging environment and deployment to production takes place if the review on staging went well. The staging app swaps to production and it becomes available on Production endpoint.

With this environment promotion approach, we build the app once and use it in both staging and production environment. The environmental configuration are done through appsettings.`<environment>`.json. We use environment variable `ASPNETCORE_ENVIRONMENT` to tell the app which appsettings.json file to read.

Depends on the technologies, the approach to support multiple environments is different. In other technologies, the environment promotion works differently.

To start off, we make some changes in the web app code to demonstrate the usage of `ASPNETCORE_ENVIRONMENT` variable in Azure App Service.

&nbsp;

# STEP 1: Make changes to application

We added a new attribute `ResultCount` in appsettings.json.

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ResultCount": 1
}
```

Similarly, we add `"ResultCount":5`in `appsettings.Development.json`

Lastly, create a new appconfigs.Staging.json file and assign value `10` to `ResultCount`.

```
- WebApp
  - appsettings.Development.json (with ResultCount = 5)
  - appsettings.json (with ResultCount = 1)
  - appsettings.Staging.json (with ResultCount = 10)
```

Next, we modify the source code to return the weather object based on the result count above. In `WeatherForecastController.cs`, add one line and change the upper bound from 5 to `resultCount`

```
int resultCount = int.Parse(Configuration["ResultCount"]);
return Enumerable.Range(1, resultCount).Select(index => new WeatherForecast
```

The changes here is to let the app to return weather objects based on the `ResultCount` configured in appsettings.json file. In Development env, we have 5 objects. Similarly, we have 10 objects in Staging and 1 object in Production.

Run and verify if the app runs OK.

&nbsp;

## STEP 2: Create App Service `staging` deployment slot

Go to Azure Portal, navigate to `webappserver` web app. Click on the Deployment slots and click add slot. Fill in `staging` and click on Add to add a deployment slot.

Once the resource is ready, go the `staging` web app's Environment variables. Add an entry of ASPNETCORE_ENVIRONMENT. Give the value of `staging`. Check the Deployment slot setting. Click on the Apply button.

The staging web app requires access to Azure Container Registry to pull the image. In summary, we have the variables below in Environment variables section.

| Variable | Value |
| --- | --- |
| ASPNETCORE_ENVIRONMENT | Staging |
| DOCKER_REGISTRY_SERVER_PASSWORD | Check the Access Keys section of container repository `acr` |
| DOCKER_REGISTRY_SERVER_URL | Check the Access Keys section of container repository `acr` |
| DOCKER_REGISTRY_SERVER_USERNAME | Check the Access Keys section of container repository `acr` |

Clik Apply button and restart the web app.

> **TIPS**: Each variable in Environment Variables has a checkbox of Deployment slot setting property. Checking the box keeps the property visible only to the current slot.

&nbsp;

## STEP 3: Deploy image to App Service `staging` deployment slot

We need to create a Service Connections in pipelines that allow us to use web app resources in Azure.

On the Azure DevOps website, go to Project Settings>Pipelines>Service Connections. Clicn on New service connection>Azure Resource Manager>Service Principal. Select the subscription & resource group. Fill in the service connection name `AzureDevOps`. Check the box to grant access permission to all pipelines.

Go to Pipelines list and click on the edit menu to edit the pipeline.

In the yaml file, add a second task like below.

```
...
    steps:
    - task: Docker@2
      displayName: Build and push an image to container registry
      inputs:
        command: buildAndPush
        repository: $(imageRepository)
        dockerfile: $(dockerfilePath)
        containerRegistry: $(dockerRegistryServiceConnection)
        tags: |
          $(tag)
    - task: AzureWebAppContainer@1
      inputs:
        azureSubscription: 'AzureDevOps'
        appName: 'app-webserver'
        containers: $(containerRegistry)/$(imageRepository):$(tag)
        deployToSlotOrASE: true
        resourceGroupName: 'rg-demo'
        slotName: staging
```

Click on Run to execute the build and deployment. Verify the logs in Build is OK. Once the process completes, check the web app log to verify the deployment is successful.

To access the deployment log of web app:

1. Go to the web app `webapp-server` on Azure Portal
2. Click on the Advanced Tools menu > Go
3. Click on Current Docker logs (Download as zip) to download the log.

Statements similar to below are found if the deployment was successful.

```
...
2024-10-08T04:10:42.724Z INFO  - Status: Downloaded newer image for acr.azurecr.io/webapp-server:21
2024-10-08T04:10:42.736Z INFO  - Pull Image successful, Time taken: 0 Seconds
...
```

Next, we verify if the service runs OK. Visit the service at the URL https://stagingxxx.azurewebsites.net/weatherforecast. The response has 10 objects because we have configured `ResultCount=10` in appsettings.Staging.json.

```
[
  {
    "date": "2024-10-09T05:23:09.8790675+00:00",
    "temperatureC": 1,
    "temperatureF": 33,
    "summary": "Balmy"
  },
  {
    "date": "2024-10-10T05:23:09.8820364+00:00",
    "temperatureC": 35,
    "temperatureF": 94,
    "summary": "Hot"
  },
  {
    "date": "2024-10-11T05:23:09.8820405+00:00",
    "temperatureC": 30,
    "temperatureF": 85,
    "summary": "Sweltering"
  },
  {
    "date": "2024-10-12T05:23:09.8820408+00:00",
    "temperatureC": 22,
    "temperatureF": 71,
    "summary": "Hot"
  },
  {
    "date": "2024-10-13T05:23:09.8820409+00:00",
    "temperatureC": 29,
    "temperatureF": 84,
    "summary": "Cool"
  },
  {
    "date": "2024-10-14T05:23:09.8820411+00:00",
    "temperatureC": 54,
    "temperatureF": 129,
    "summary": "Scorching"
  },
  {
    "date": "2024-10-15T05:23:09.8820412+00:00",
    "temperatureC": 8,
    "temperatureF": 46,
    "summary": "Mild"
  },
  {
    "date": "2024-10-16T05:23:09.8820414+00:00",
    "temperatureC": 50,
    "temperatureF": 121,
    "summary": "Hot"
  },
  {
    "date": "2024-10-17T05:23:09.8820415+00:00",
    "temperatureC": 26,
    "temperatureF": 78,
    "summary": "Chilly"
  },
  {
    "date": "2024-10-18T05:23:09.8820417+00:00",
    "temperatureC": 50,
    "temperatureF": 121,
    "summary": "Hot"
  }
]
```

&nbsp;

## STEP 4: Swap the slot to production

> **NOTES**: To have a clean configuration, it is good to remove the acr credential in `webappserver` environmental variables. The web app will now get the latest deployment from staging deployment slot instead of Azure Container Registry.

Before the swapping takes place, it is good to make sure the there is no `ASPNETCORE_ENVIRONMENT` variable in `webappserver` environment variables section. By default, the web app uses appsettings.json and the expected `ResultCount` is 1. The service shall return 1 object.

Let's say the review on staging environment went well, we are ready to swap the slot to production to complete the deployment.

Go to Deployment slots menu of `webappserver` web app . Click on Swap button.

Make sure the Source has the staging web app and the target points to the current `webappserver` web app. The Config Changes should have no changes at all. All configurations shouldn't be brought over to the target.

Click Start Swap button to begin the swapping. It will take a while to complete the process.

Once the swap completes, visit the webappserver URL `https://xxx.azurewebsites.net/weatherforecase` to verify the result. The response returns 1 object.

```
[{"date":"2024-10-09T07:00:52.5667431+00:00","temperatureC":-10,"temperatureF":15,"summary":"Cool"}]
```

&nbsp;


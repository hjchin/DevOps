# Publish containerised app to Azure App Service

Basing the image we have built in previous step, this document explains how to publish the containerised app to Azure App Service.

There are 2 Azure services are invovled namely Azure Container Registry and Azure App Service. The steps can be break down into below.

Due to conveniences, some tasks below are achieved using Azure web portal while others are done using Azure CLI.

&nbsp;

## STEP 0: Get Ready

We use Azure Web portal and Azure CLI in the journey below. [Download](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli), install and verify azure cli is running OK.

```
webapp> az 

     /\
    /  \    _____   _ _  ___ _
   / /\ \  |_  / | | | \'__/ _\
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!
```

&nbsp;

## STEP 1: Create Azure Container Registry (ACR)

Using azure web portal, we create a container registry which going to house the images.

Go to marketplace, search for Container Registry. Create a container registry `acr` with default values.

Go to the resource to verify the newly created container registry. Check under the repositories menu, the list is empty. The repositories is where images are stored.

&nbsp;

## STEP 2: Push image to ACR

We start with azure login in CLI. Run command `az login` and complete the auzre login.

The azure container registry require login. To do that, we run command

```
az acr login -n acr
Login Succeeded
```

> NOTES: Make sure the Docker Desktop is running when running the command above. It requires docker for the login

In order to push the image to container registry, we need to tag the image to the remote registry's image name. it works similar to the GIT.

```
docker tag webapp-server arc.azurerc.io/webapp-server
```

and run command

```
docker push arc.azurerc.io/webapp-server
Using default tag: latest
The push refers to repository [acrcorpitnpdsea01.azurecr.io/webapp-server]
b85649901a16: Pushed
dd45aa26a708: Pushed
2fd8e7035fbf: Pushed
830de0460a89: Pushed
9dcfe0b206d7: Pushed
c0bf2214dbfa: Pushed
63ca1fbb43ae: Pushed
latest: digest: sha256:c76e8b7966b42d7267c47f26da758a1b4ea37126fce558a6d9e2d4abdfcc05b2 size: 1786
```

The push completes with the message above. We go to the Azure portal and verify the image under container registry's repositories menu.

&nbsp;

## STEP 3: Create Azure App Service with Container Deployment

Go to marketplace, search for Web App. Click the button Create to open the create steps.

Fill in all required fields with values below.

| Tab | Field| Value |
| --- | --- | --- | 
| Basic | Publish | Container |
| Basic | Operating System | Linux |
| Container | Image Source | Azure Container Registry |
| Container | Registry | acr |
| Container | Image | webapp-server |
| Container | Tag | latest |

Review all fields on Review tab and create the `webappserver` app.

Use browser and visit the web app page. Append the `/weatherforecast` to URL to have `https://xxx.azurewebsites.net/weatherforecase`. It invoked API returns result like below

```
[{"date":"2024-09-27T11:04:27.696441+08:00","temperatureC":49,"temperatureF":120,"summary":"Cool"},
{"date":"2024-09-28T11:04:27.696451+08:00","temperatureC":51,"temperatureF":123,"summary":"Scorching"},
{"date":"2024-09-29T11:04:27.696451+08:00","temperatureC":-19,"temperatureF":-2,"summary":"Cool"},
{"date":"2024-09-30T11:04:27.696452+08:00","temperatureC":14,"temperatureF":57,"summary":"Cool"},
{"date":"2024-10-01T11:04:27.696452+08:00","temperatureC":22,"temperatureF":71,"summary":"Freezing"}]
```

&nbsp;

## STEP 4: Using logs

The logs of the application and container are emitted by docker are found in Azure Web App's Deployment Center.

Go to Depolyment Center and clicks Logs tab. You will see the container logs and application logs here.


&nbsp;


## Reference

1. [Deploy and Run a containerized web app with Azure App Service](https://learn.microsoft.com/en-us/training/modules/deploy-run-container-app-service/)


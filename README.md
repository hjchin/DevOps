# DevOps

This documents a journey of app containerisation and cloud native application migration.

The journey is divided to several documents and each covers single topic. We first touch on how to containerize a web app in local development. The second step shows how to publish Azure Web app for Container. We then explain how to automate the publish task with Azure DevOps. Lastly, we finish the first section with web app environment promotion.

In section 2, we quickly explain how container helps in developing app in a team. We have not touch on how container beneficial to application development in previous section. We dedicate section 2 for this purpose. 

&nbsp;

## Section 1

1. [Containerize a web api application with Docker](WebApp/README.md)
2. [Publish containerized app to Azure App Service](README.AppService.md)
3. [Build CI/CD with Azure DevOps Pipeline](README.Pipeline.md)
4. [Promote Web App from Staging to Production](README.EnvPromotion.md)

&nbsp;

## Section 2

1. [Develop with container](WebApp/README.Develop.md)

&nbsp;

## Key technologies
Key technologies used in this document are:

* asp.net webapp
* Docker Desktop
* Azure
  * App Service (Azure Web App for Container)
  * Container Registry
  * DevOps
* Kubernetes
* cURL
* GitHub

The key tool used in this document is VSCODE and terminal.

&nbsp;

## Background

The technologies preference and the approach are chosen based on environmental settings. The journey follows what already in production environment, it is meant to be used in future cloud native application migration.

&nbsp;


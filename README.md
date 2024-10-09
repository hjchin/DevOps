# DevOps

This documents a journey of app containerisation and cloud native application migration.

Key technologies used in this document are:

* asp.net webapp
* Docker
* Azure
  * App Service (Azure Web App for Container)
  * Container Registry
  * DevOps
* Kubernetes
* cURL
* GitHub

This document covers key topics:

* Containerise an existing web api app with Docker
* Make image available in Azure Container Registry (ACR)
* App Service deployment with ACR's image

The key tool used in this document is VSCODE and terminal.

&nbsp;

## Background

The technologies preference and the approach are chosen based on environmental settings. The journey follows what already in production environment, it is meant to be used in future cloud native application migration.

&nbsp;

## Reading Guide

The journey is divived to several documents for better readability. We first touch on how to containerize a web app in local development. The second step shows how to publish Azure Web app for Container. We then explain how to automate the publish task with Azure DevOps. 

1. [Containerize a web api application with Docker](WebApp/README.md)
2. [Publish containerized app to Azure App Service](README.AppService.md)
3. [CI/CD with Azure DevOps Pipeline](README.Pipeline.md)
4. [Promote Web App from Staging to Production](README.EnvPromotion.md)

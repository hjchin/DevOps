# DevOps

This documents a journey of app containerisation and cloud native application migration.

Key technologies used in this document are:

* asp.net webapp
* Docker
* Azure
  * Azure App Service (Azure Web App for Container)
  * Azure Container Registry
* Kubernetes
* cURL

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

The journey is divived to several documents for better readability. We first touch on how to containerise a web app in local development. The second step shows how to publish Azure Web app for Container. 

1. [Containerise a web api application with Docker](WebApp/README.md)
2. [Publish containerised app to Azure App Service](README.AppService.md)


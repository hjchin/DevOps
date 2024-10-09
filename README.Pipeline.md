# CI/CD with Azure DevOps Pipeline

Previously we had published the containerized app to App Service using docker commands. In this document, we take a step further by automate some publishing tasks using CI/CD tool.

We use Azure DevOps Pipeline for CI/CD. Pipeline could include unit testing, statistic code analysis, etc. in it. We use it for deployment task in this document.

&nbsp;

# STEP 1: Push the image to Azure Container Repository

Go to Azure DevOps and click on Pipeline menu. Click on the new pipeline button and follows the steps below.

1. Pick a repository service provider `Github` and the repo `DevOps`
2. Configure it to use Docker (Build and push an image to Azure Container Registry)
3. Select the Azure Container Registry `acr` in use
4. Give a name to the image `webapp-server`to push on ACR

&nbsp;

Pipelines create the yaml file below at the end of the steps.

```
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
- master

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '1c0e5b07-8e9f-4f19-ae62-706877961536'
  imageRepository: 'webapp-server'
  containerRegistry: 'acr.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/WebApp/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
  vmImageName: 'ubuntu-latest'

stages:
- stage: Build
  displayName: Build and push stage
  jobs:
  - job: Build
    displayName: Build
    pool:
      vmImage: $(vmImageName)
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
```

`variables` section defines the variables usd in subsequent sections. `$(imageRepository)` and `$(dockerfilePath)` are some variable examples in `stages` section.

The `dockerRegistryServiceConnection` has been created for us during the wizard process. The credential information of `dockerRegisteryServiceConnection` is kept safe outside the pipeline. We have only the ID in the pipeline yaml file. The service connection variables are stored in Project settings > Service connections.

> **TIPS:** Check the variable `imageRepository` and verify the image name you wish to use in container repository. The field in the wizard might remove character `-`.

There is only one step in the yaml currently. It builds and pushes an image to container registry.

Click the Save and run button to test and verify the outcome. Check the logs in `Build` to see the process.

```
- Build
  - Initialize Job
  - Checkout hjchin/DevOps
  - Build and push an image to container registry
  - Post-job: Checkout hjchin/DevOps master 
  - Finalize Job
```

> **NOTES**:
> 
> - The creation of new pipeline creates azure-pipeline.xml file at the root directory of the source code.
> - Any changes to the remote branch (PR merge/direct push) will trigger pipeline to build.

---

To verify the build outcome, go to `acr` Azure Repository and you will find image `webapp-server` in the list.

&nbsp;

## References

[Pipeline Yaml Schema](https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/pipeline?view=azure-pipelines)


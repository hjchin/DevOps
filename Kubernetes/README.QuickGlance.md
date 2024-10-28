# Quick Glance on Kubernetes

It is necessary to take a look on the K8s commands before proceed further in this section. We also touch on the relationship of Docker and k8s, and how they work together in DevOps.

> **NOTES:** k8s is a short hand for kubernetes. I use Kubernetes in headings but prefer to keep it short in paragraphs. They are identical.

As K8s in production is likely a managed service, we do not work on the specification or configuration file directly. In other words, there are less code snippets going forward. Thus, before going further, it is important to understand the foundamental.

&nbsp;

## Docker and Kubernetes

We used Docker to solve problems below:

1. To provide consistency between development environment and production environment.
2. To provide a manageable approach for dependencies upgrade.
3. To speed up the deployment process using verified image.
4. To speed up the development environment for new comer in a project.

The output of containerization, the image file, is the **INPUT** for container orchestration automation. Container registry is a common place to hold the images to be consumed by k8s. Some basic understandings of Docker could help in learning and using k8s.

> **NOTES:** The term container orchestration is referring to a series of tasks for managing container life cycle.

In these documents, we use k8s to solve problems below:

1. Load balancing with autoscaling
2. 0 downtime deployment

The k8s of production environment and local environment use different technologies on most common cases. In production, we likely go for managed k8s services. Cloud service providers offer many managed services and we use use Azure Kubernetes Service, AKS, in this series of documents, In local environment for learning purpose, we could use a local k8s known as [minikube](https://minikube.sigs.k8s.io/docs/).


&nbsp;

## Kubernetes Basic Concepts & `kubectl` Commands

K8s cli command, `kubectl`, offers a list of rich commands we could use to interact with k8s cluster. A cluster is a set of worker machines, called nodes, that run containerised applications.

1. To deploy a cluster, we use command `kubectl create` or `kubectl apply` on a deployment resource. `kubectl apply` is a declarative way to manage resources whereas `kubectl` is an imperative way to manage resources.
2. To explore resources in a cluster, we use command
   1. `kubectl get` to list resources. For instace, command `kubectl get deployments` list all deployments
   2. `kube describe` to show detailed information of a resource.
   3. `kubectl logs` to print the logs from a container.
   4. `kubectl exec` to execute a command in a container.
3. To expose the containerized app to external traffics, we use command `kubectl expose`.

Those commands above are the common and useful tools for troubleshooting. There are some concept similarities between `docker` commands and `kubectl` commands. The way kubectl commands and docker commands designed are alike.

Diagram below shows a simple relationship between cluster, node, pod and container.

```
__________________________________
|                                 |
|             Cluster             |                  
|   ___________________________   |
|  |                           |  |            
|  |           Node            |  |
|  |    ___________________    |  |                      
|  |   |                   |   |  |
|  |   |        Pod        |   |  |
|  |   |   _____________   |   |  |
|  |   |  |             |  |   |  |
|  |   |  |  Container  |  |   |  |
|  |   |  |_____________|  |   |  |
|  |   |                   |   |  |
|  |   |___________________|   |  |
|  |                           |  |
|  |___________________________|  |
|                                 |
|_________________________________|
```

A node could be physical or virtual machine that has a same IP for all pods in it. 

&nbsp;

## Reference

1. [Learning Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)


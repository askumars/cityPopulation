# cityPopulation Apps
cityPopulationCode

# Helm Chart Templates for Node.js in Kubernetes

<a href='http://CloudNativeJS.io/'><img src='https://img.shields.io/badge/homepage-CloudNativeJS-blue.svg'></a>

This project provides template Helm Charts for deploying a Node.js web application into any Kubernetes based cloud.

The templates require your application to built into a Docker image. The [Docker Templates](http://github.com/CloudNativeJS/docker) project provides assistance in creating an image for your application.

This project provides the following files:

| File                                              | Description                                                           |
|---------------------------------------------------|-----------------------------------------------------------------------|  
| `/chart/nodeserver/Chart.yaml`                    | The definition file for your application                           | 
| `/chart/nodeserver/values.yaml`                   | Configurable values that are inserted into the following template files      | 
| `/city-population-helm-chart/templates/basedeployment.yaml` | Template to configure your application deployment.                 |
| `/city-population-helm-chart/templates/deployment.yaml`     | Template to configure your application deployment.                 | 
| `/city-population-helm-chart/templates/service.yaml`        | Template to configure your application deployment.                 | 
| `/city-population-helm-chart/templates/hpa.yaml`            | Template to configure your application deployment.                 | 
| `/city-population-helm-chart/templates/istio.yaml`          | Template to configure your application deployment.                 | 
| `/city-population-helm-chart/templates/NOTES.txt`           | Helper to enable locating your application IP and PORT        | 

In order to use these template files, copy the files from this project into your application directory. You should only need to edit the `Chart.yaml` and `values.yaml` files.

## Prerequisites

Using the template Helm charts assumes the following pre-requisites are complete:  

1. You have a Kubernetes cluster  
  This could be one hosted by a cloud provider or running locally, for example using [Minikube](https://kubernetes.io/docs/setup/minikube/)
  
2. You have kubectl installed and configured for your cluster  
  The [Kuberenetes command line](https://kubernetes.io/docs/tasks/tools/install-kubectl/) tool, `kubectl`, is used to view and control your Kubernetes cluster.

3. You have the Helm command line and Tiller backend installed  
   [Helm and Tiller](https://docs.helm.sh/using_helm/) provide the command line tool and backend service for deploying your application using the Helm chart.
   These charts are compatible with Helm v2 and v3

4. You have created and published a Docker image for your application  
The Docker Template project provides guidance on [building a run image](https://github.com/CloudNativeJS/docker#using-dockerfile-tools) for your application and [publishing it to the DockerHub registry](https://github.com/CloudNativeJS/docker#publishing-the-image).

5. Your application has a "health" endpoint  
  This allows Kubernetes to restart your application if it fails or becomes unresponsive. The [Health Connect](https://github.com/CloudNativeJS/cloud-health-connect) middleware can be used to add a health endpoint.

## Adding the Chart to your Application

In order to add Helm Charts to your application, copy the `charts` directory from this project into your application's root directory.

You then need to make a single change before the charts are usable: to set the `image.repository` for your application.

### Setting the `image.repository` parameter

In order to change the `image.repository` parameter, open the `city-population-helm-chart/values.yaml` file and change the following entry:  

```sh
image:
  repository: askumars/city-population:
```
to set `<namespace>` to your namespace on DockerHub where you published your application as `city-population-helm-chart`.

## Configuring the Chart for your Application

The following table lists the configurable parameters of the template Helm chart and their default values.

| Parameter                  | Description                                     | Default                                                    |
| -----------------------    | ---------------------------------------------   | ---------------------------------------------------------- |
| `image.repository`         | image repository                                | `askumars/city-population`                                 |
| `image.tag`                | Image tag                                       | `latest`                                                    |
| `image.pullPolicy`         | Image pull policy                               | `IfNotPresent`                                                   |
| `livenessProbe`   | Configuration for any liveness probe provided |   YAML object of liveness probe. See [Liveness and Readiness Probes](#liveness-and-readiness-probes)                            |
| `readinessProbe`         | Configuration provided for any liveness probe provided      | YAML object of readiness probe. See [Liveness and Readiness Probes](#liveness-and-readiness-probes)         |
| `service.name`             | Kubernetes service name                                | `Node`                                                     |
| `service.type`             | Kubernetes service type exposing port                  | `NodePort`                                                 |
| `service.port`             | TCP Port for this service                       | 8080                                                       |
| `resources.limits.memory`  | Memory resource limits                          | `128m`                                                     |
| `resources.limits.cpu`     | CPU resource limits                             | `100m`                                                     |


## Using the Chart to deploy our Application to Kubernetes

In order to use the Helm chart to deploy and verify your application in Kubernetes, run the following commands:

1. From the directory containing `Chart.yaml`, run:  


  **Helm v3**
  
  ```sh
  helm install release-1.0 city-population/
  ```

  This deploys and runs your application in Kubernetes, and prints the following text to the console:  
  
  ```sh
  Congratulations, you have deployed your Application to Kubernetes using Helm!

  To verify your application is running, run the following two commands to set the SAMPLE_NODE_PORT and SAMPLE_NODE_IP environment variables to the location of your application:

[root@master-node ~]# helm install release-1 city-population/
NAME: release-1
LAST DEPLOYED: Fri Dec 23 07:35:56 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=city-population,app.kubernetes.io/instance=release-1" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT

You application should now be visible in your browser.


## Uninstalling your Application
If you installed your application with:  

**Helm v2**

```sh
helm install --name city-population .
```
then you can:

* Find the deployment using `helm list --all` and searching for an entry with the chart name "nodeserver".
* Remove the application with `helm delete --purge city-population`.

**Helm v3**

```sh
helm install release-1.0 city-population/
```
then you can:

* Find the deployment using `helm list --all` and searching for an entry with the chart name "city-population".
* Remove the application with `helm uninstall city-population`.

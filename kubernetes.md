# Kubernetes

## Windows test cluster setup

On Windows we can easily install a single-node Kubernetes cluster using Minikube: [https://github.com/kubernetes/minikube](https://github.com/kubernetes/minikube)

In addition to control the cluster we can use kubectl: [https://kubernetes.io/docs/tasks/tools/install-kubectl/](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Steps:

* download minikube and add to the path
* download kubectl and add to the path
* enable VT extensions in the BIOS \(CPU config\)
* install Hyper-V on W10+
* create a vSwitch for Minikube
  * Type: External
  * Name: Minikube
  * ! Make sure to associate it with the correct physical NIC!
* execute the following command
  * minikube start --vm-driver "hyperv" --hyperv-virtual-switch "Minikube" --disk-size 10g --memory 4096 --v 9999 --alsologtostderr

Useful commands

* minikube start: start k8s
* minikube docker-env: configure the Docker environment variables
* minikube service list

Alternative path to install:

* enable W10+ developer mode
* install WSL \(subsystem for Linux\)
* install through Ubuntu apt-get :\)

## Helm

Helm \([https://github.com/kubernetes/helm/releases](https://github.com/kubernetes/helm/releases)\) is a "package manager" for Kubernetes, allowing to easily deploy "Charts" \(pre-made Deployments\)

Installation:

* download the release and add to the path
* run helm init

Use other helm commands to list packages, update the metadata and install/remove charts.

Available charts \(stable & unstable\): [https://github.com/kubernetes/charts](https://github.com/kubernetes/charts)




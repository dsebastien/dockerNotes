# Kubernetes

## Intro

Born at Google!

Ancestors: Borg & Omega

Kubernetes = greek for "Helmsman" \(person who steers a ship\)

Short name: k8s \(8 letters between the K and S of Kubernetes :p\).

## Components & concepts

### Masters

Multi-master model.

Master parts:

* apiserver {} \([kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/)\)
  * API of the control plane of the cluster
  * REST API
  * consumes JSON \(via manifest files\)
* cluster store
  * key-value store containing all the config and state of the cluster
  * source of truth for the cluster
  * etcd used by default \(distributed, consistent, watchable, ...\)
* controller manager \([kube-controller-manager](https://kubernetes.io/docs/reference/generated/kube-controller-manager/)\)
  * controller of controllers
    * node controller
    * endpoints controller
    * namespace controller
  * all of these watch for changes and ensure that the current state of the cluster matches the desired state
* scheduler \([kube-scheduler](https://kubernetes.io/docs/reference/generated/kube-scheduler/)\)
  * watches apiserver for new pods
  * assigns work to nodes
    * affinity/anti-affinity
    * constraints
    * resource management
    * ...

Usually we interact with the control plane using kubectl which talks \(using JSON\) with the apiserver.

### Nodes

Kubernetes nodes \(aka minions\): workers.

* kubelet \([https://kubernetes.io/docs/reference/generated/kubelet/](https://kubernetes.io/docs/reference/generated/kubelet/)\)
  * main agent of the node
  * registers the node with the cluster
  * watches apiserver for work assignments
  * instantiates pods
  * reports back to the master
  * **exposes an endpoint on port :10255**
    * /spec
    * /healthz
    * /pods
  * authentication: [https://kubernetes.io/docs/admin/kubelet-authentication-authorization/](https://kubernetes.io/docs/admin/kubelet-authentication-authorization/)
* container engine \(runtime\)
  * pulling images
  * starting/stopping containers
  * pluggable
    * Docker
    * Rocket \(rkt\)
* kube proxy \([https://kubernetes.io/docs/reference/generated/kube-proxy/](https://kubernetes.io/docs/reference/generated/kube-proxy/)\)
  * network brains of the node
  * makes sure that each nodes gets its own unique IP
    * all containers in a pod share a single IP
  * load balances across all pods in a _service _\(a service hides multiple pods behind a single network address\)

### Declarative model and desired state

Declarative model

* we give the apiserver manifest files \(YAML or JSON\) to explain what the cluster should look like
  * kubernetes makes changes to reach the desired state

### Pods

Atomic unit of scheduling/scaling. Containers are ALWAYS inside a Pod.

* can contain 1-n containers
* sandbox for containers \(environment\)
  * contains a network stack \(transport, net, link\)
  * kernel namespaces
  * ...
* all containers in a pod share the pod environment
  * same IP, same network stack, same IPCs, same access to volumes, ...
* atomic: pods are deployed atomically \(all or nothing\)

Lifecycle

* pending
* running
* succeeded / failed

Example:

* Web server
* Log scraper \(sidecar container\)

Manifest example:

```
apiVersion: v1
kind: Pod
metadata:
  name: hello-pod
  labels:
    zone: prod
    version: 1.0.0
spec:
  containers:
  - name: hello-container
    image: nigelpoulton/pluralsight-docker-ci:latest
    ports:
    - containerPort: 8080
```

Deployment of that pod:

```
kubectl create -f pod.yml
kubectl describe pods
```

### Services

Services provide a stable IP & DNS name for a set of pods.

We can't rely on pod IPs. When pods die, they're replaced by other pods on \(potentially\) different nodes, with different IPs. Same when scaling up/down.

* kubernetes object described by a YAML/JSON manifest like all other Kubernetes objects
* load balances requests made to the pods
  * by default: random
  * DNS round robin possible, but be wary -- cache TTLs, ...\)
* pods are associated with a service based on labels
* can configure session affinity
* only send requests to healthy pods
* can point to things outside the cluster
* uses TCP by default

### Labels rock :\)

### Deployment

Declarative approach: describe how the cluster should look like.

* described by a YAML/JSON manifest
  * spec-once deploy-many
  * self-documenting
  * versioned
* extend the feature set of replication controller
* simple rolling updates/rollbacks
  * multiple concurrent versions
    * blue-green deployments
    * canary releases

# Tools

## kubectl

To control the cluster we can use kubectl: [https://kubernetes.io/docs/tasks/tools/install-kubectl/](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Helm

Helm \([https://github.com/kubernetes/helm/releases](https://github.com/kubernetes/helm/releases)\) is a "package manager" for Kubernetes, allowing to easily deploy "Charts" \(pre-made Deployments\)

Installation:

* download the release and add to the path
* run helm init

Use other helm commands to list packages, update the metadata and install/remove charts.

Available charts \(stable & unstable\): [https://github.com/kubernetes/charts](https://github.com/kubernetes/charts)

# Minikube

## About

Best way of spinning up a local k8s environment.

* kubectl interacts with the Minikube VM
* Minikube VM
  * Localkube \(binary\) containing
    * Master
    * Node
  * Container runtime
    * Docker or Rocket \(rkt\)

## Windows test cluster setup

On Windows we can easily install a single-node Kubernetes cluster using Minikube: [https://github.com/kubernetes/minikube](https://github.com/kubernetes/minikube)

Steps:

* download minikube and add to the path
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

## 




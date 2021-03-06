# Labs Workbench Developer Startup

This repository contains startup scripts to run the Labs Workbench services on a
single developer node. This includes:
* Small Kubernetes cluster via Docker containers
* Nginx ingress controller
* Labs Workbench etcd and SMTP servers
* Labs Workbench Angular UI and REST API Server
* Cloud9 IDE for development

## Minimum System Requirements
* 2 CPUs
* 2+ GB RAM
* 40+ GB storage

## Prerequisites
* Git
* Docker 1.9+ or Minikube
* Wildcard DNS or /etc/hosts entry

## Getting Started
Clone this repo, then set up desired instance parameters by editing the
following ConfigMap:
```
git clone https://github.com/nds-org/ndslabs-startup
cd ndslabs-startup/
vi templates/config.yaml
```

### Configuration Options
Within `templates/config.yaml` you can customize your instance of workbench with
several options. The only required change is to set `workbench.domain` domain to
match wildcarded domain hosted on your server.

In the case where a local bind is needed, you'd need to set `workbench.ip` to
point an IP that your browser can access, then modify your local /etc/hosts and
DNS settings to point to this same IP).

### Advanced Customization
For futher customization, you can fork the entire [ndslabs
repo](https://github.com/nds-org/ndslabs) and point these config options to your
new fork, allowing you to override anything in the UI source code.
```
# Drop-in a customized UI from git (custom CSS/HTML, new views, additional
functionality, etc)
git.dropin_repo: ""
git.dropin_branch: ""
```

## Kubernetes
There are multiple ways to run a local single-node Kubernetes cluster.

Two of the most popular methods are
[MiniKube](https://github.com/kubernetes/minikube) and Hyperkube.

### Available Commands
* `./kube.sh`: Bring up a local Kubernetes cluster with
[hyperkube](https://github.com/kubernetes/community/blob/master/contributors/devel/local-cluster/docker.md)
which uses Docker to run the other Kubernetes microservices as containers.
* `./kube.sh down`: Bring down all Kubernetes services and deletes all leftover
Kuberenetes containers
* `./kube.sh basic-auth`: Generate a new basic-auth secret for use with the
development environment (see below)
* `./kube.sh deploy-tools`: (DEPRECATED) Shortcut for running an
ndslabs/deploy-tools container

#### Via Minikube
Minikube will run a local VM on your host machine that provides the Kubernetes
services installed and running.

First, you will need to download the [minikube](https://github.com/kubernetes/minikube)
binary for your OS. Then, to start a local Kubernetes via minikube, simply run:
```
minikube start
```

To stop Kubernetes via minikube:
```
minikube stop
```

#### Via Hyperkube
Hyperkube will run a local Kubernetes cluster in several Docker containers all
running on the host.

To start a local Kubernetes via hyperkube, simply run our provided `./kube.sh`:
```
./kube.sh
```

With no command passed, this will automatically start all necessary Kubernetes
services running as separate Docker containers.

## Labs Workbench
To evaluate the Labs Workbench platform, simply run `./ndslabs.sh up`

This will start all of the required Labs components in your local Kubernetes
cluster.

NOTE: assumes wildcard DNS is available, but you can add individual /etc/hosts
entries if needed.

### Available Commands
* `./ndslabs.sh up`: Start workbench services
* `./ndslabs.sh down`: Bring down all workbench services
(but leaves Kubernetes running)
* `./ndslabs.sh print-passwd`: Print the Admin Password of the currently running
ndslabs-apiserver pod to the console


### Command line options
* `--no-ui`: Start up the API and supporting services, but don't launch the web
service
* `--start-bind`: Start up a local bind server to host wildcard domains on the
local workstation. This is not needed if you are running with an external DNS
that has wildcard domains (`*.foo.com`) pointing to the host that is running
the workstation.


## Development Environment (Optional)
```
./devenv.sh
```

With no command passed, this will automatically generate a basic-auth secret and
start up a Cloud9 IDE to use to for development. It will then replace the
running instace of ndslabs-webui with a version that reflects changes made
dynamicly from within Cloud9.

### Available Commands
* `./devenv.sh`: Start Kubernetes and Labs Workbench services, then bring up a
development environment to modify the UI source
* `./devenv.sh down`: Bring down development environment and swap running UI
with static image

# Gotchas
* Your node must have the label **ndslabs-role-compute=true** in order for the
* Labs Workbench API server to successfully schedule services there. NOTE: the
* `./ndslabs.sh up` script handles this for you, by default.

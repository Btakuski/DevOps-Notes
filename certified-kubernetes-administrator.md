---
title: "Certified Kubernetes Administrator"
tags: "Tutorial"
---

# Certified Kubernetes Administrator

___
## Core Concepts

### Cluster Architecture 


**Worker Nodes:** Host application as containers

**Master Node:** Manage, plan, schedule, monitor nodes 

**ETCD Cluster:** Database that stores information in a _key:value_ format 

**kube-scheduler:** Identifies the right node to place a container on

**Controller-Manager:** A daemon that embeds the core control loops shipped with Kubernetes. Control loop that watches the shared state of the cluster through the `apiserver` and makes changes attempting to move the current state towards the desired state 

**Node-Controller:** Kubernetes control plane component that manages various aspects of nodes. 
e.x: Onboards new nodes to the cluster, monitors node health

**Replication-Controller:** Ensures that the desired number of containers are running at all times in a replication group 

**kube-apiserver:** Primary management component of Kubernetes. Responsible for orchestrating all operations within the cluster. It exposes the Kubernetes API which is used by external users to perform management operations on the cluster as well as the various controllers. 

**Container Runtime Engine:** You need a container runtime in each node in the cluster so that the Pods can run there

ex: 
  - containerd
  - CRI-O
  - Docker

**kubelet:** An agent that runs on each node in a cluster. It listens for instructions from the `kube-apiserver` and deploys or destroys containers on the node as required

**Kube-proxy:** Ensures that the necessary rules are in-place on the worker nodes to allow the containers running on them to communicate 

Master Node
- etcd cluster
- kube-apiserver
- Kube Controller Manager
- kube-schedulers 


Worker Node
- kubelet
- Kube-proxy
- Container Runtime Engine

### ETCD 
- What is ETCD?
  - A distributed reliable key-value store that is simple, secure & fast
- What is a Key-Value Store?
  - Stores information in a key:value format
  - Used to store and receive small chunks of data that requires fast read/writes
- How to get started quickly? 
  1. Download binaries
  2. Extract them
  3. Run ETCD service
- How to operate ETCD?
  - ETCD service listens on port: 2379 by default
  - Attach clients on ETCD service to store/retrieve information
  - ETCD control client: `etcdctl`
  - `./etcdctl set key1 value1`
  - `./etcdctl get key`

#### ETCD in Kubernetes
- ETCD stores informationr regarding the cluster:
  - Nodes
  - Pods
  - Configs
  - Secrets
  - Accounts
  - Roles
  - Bindings
  - Others
- Every change you make to your cluster are updated in the ETCD server
- ETCD is deployed:
  - Manually
    - Download ETCD binaries and configuring ETCD as a service in the master node
  - kubeadm
    - `kubeadm` deploys ETCD as a pod in your kube-system namespace
- ETCD in HA environment
  - Multiple master nodes in your cluster
  - Multiple ETCD instances spread across the master nodes

### Kube API Server
- The primary management component of Kubernetes
- Responsible for:
  1. Authenticating user
  2. Validating request
  3. Retrieving data
  4. Updating ETCD 
  5. Scheduling

- View api-server options:
  - `cat /etc/kubernetes/manifests/kube-apiserver.yaml` (kubeadm)
  - `cat /etc/systemd/system/kube-apiserver.service` (manual)
  - `ps -aux | grep kube-apiserver`

### Kube Controller Manager
- Manages the various controllers in Kubernetes:
  1. Watch status
  2. Remediate situation
- Controller: A process that continuously monitors the state of various components within the system and works towards bringing the entire system to its desired state. ex:
  - Node-Controller: responsible for monitoring the status of the nodes and taking necessary actions to keep the application running (via kube-apiserver) 
    - Options:
      - `node-monitor-period=5s`
      - `node-monitor-grace-period=40s`
      - `pod-eviction-timeouit=5m0s`
  - Replication-Controller: responsible for monitoring the status of replicasets and ensuring the desired number of pods are available at all times within the set 
- Installation:
  1. Download kube-controller-manager
  2. Run it as a service
- View kube-controller-manager options:
  - `cat /etc/kubernetes/manifests/kube-controller-manager.yaml` (kubeadm)
  - `cat /etc/systemd/system/kube-controller-manager.service`
  - `ps -aux | grep kube-controller-manager`

### Kube Scheduler
- Responsible for scheduling pods on nodes
- Only responsible for _deciding_ which pod goes on which node
- Process:
  1. Filter nodes
  2. Rank nodes
- View kube-scheduler options:
  - `cat /etc/kubernetes/manifests/kube-scheduler.yaml` (kubeadm)
  - `ps -aux | grep kube-scheduler` 

### Kubelet
- Primary "node agent" that runs on each node
- Registers the node with the `apiserver`
- Process:
  1. Register Node
  2. Create Pods
  3. Monitor Node & Pods 
- `kubeadm` does not automatically deploy Kubelets, they must be manually installed
- View kubelet options:
  - `ps -aux | grep kubelet` 

### Kube Proxy
- A process that runs on each node in the Kubernetes cluster
- Its job is to look for new services, and everytime a new service is created, it creates the appropriate rules on each node to forward traffic to those services to the backend pods (ex: using ip tables for routing)  
- Pod Network: Internal virtual network that spans all the nodes in a cluster to which all the pods can connect to. Using this network, pods can communicate with each other across nodes
- View kube-proxy pods:
  - `kubectl get daemonset -n kube-system` 
___

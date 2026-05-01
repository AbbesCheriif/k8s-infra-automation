# Kubernetes Cluster Automation with Vagrant & Kubeadm

## Project Overview

This project demonstrates how to automatically provision a **multi-node Kubernetes cluster** using:

* **Vagrant**
* **VirtualBox**
* **Kubeadm**
* **Shell automation (`deploy.sh`)**

The goal of this repository is to provide a **simple and reproducible Kubernetes lab environment** suitable for learning Kubernetes administration and DevOps practices.

Vagrant is responsible for creating virtual machines, while the `deploy.sh` script prepares each node with all required dependencies for Kubernetes installation.

---

## Architecture

The cluster consists of **three virtual machines**:

| Node    | Role          | IP Address    |
| ------- | ------------- | ------------- |
| master  | Control Plane | 192.168.56.10 |
| worker1 | Worker Node   | 192.168.56.11 |
| worker2 | Worker Node   | 192.168.56.12 |

Each machine is automatically configured with:

* Correct hostname
* Required kernel modules
* Networking configuration
* Container runtime (**containerd**)
* Kubernetes components:

  * kubelet
  * kubeadm
  * kubectl

---

## Automation Strategy

Automation is handled using a single provisioning script:

```
deploy.sh
```

The script performs:

* System preparation
* Swap disabling
* Kernel configuration
* Containerd installation & configuration
* Kubernetes repository setup
* Kubernetes tools installation

After provisioning, all nodes are **ready to form a Kubernetes cluster**.

---

## Repository Structure

```
.
├── Vagrantfile
└── deploy.sh
```

---

## Requirements

Before starting, ensure you have installed:

* Vagrant
* VirtualBox

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

---

### 2. Start the infrastructure

```bash
vagrant up
```

Vagrant will:

* Create the 3 virtual machines
* Execute `deploy.sh` automatically on each node
* Install all Kubernetes prerequisites

---

## 3. Kubernetes Cluster Initialization

After provisioning completes, only the Kubernetes bootstrap steps remain.

---

### 3.1 On the **Master Node**

Connect to the master:

```bash
vagrant ssh master
```

Initialize the cluster:

```bash
sudo kubeadm init \
  --apiserver-advertise-address=192.168.56.10 \
  --pod-network-cidr=192.168.0.0/16
```

---

### Configure kubectl

```bash
mkdir -p /home/vagrant/.kube
sudo cp /etc/kubernetes/admin.conf /home/vagrant/.kube/config
sudo chown vagrant:vagrant /home/vagrant/.kube/config
```

---

### Install the Pod Network (Calico)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/calico.yaml
```

---

### Generate Worker Join Command

```bash
kubeadm token create --print-join-command
```

Copy the generated command.

---

### 3.2 On Each Worker Node

Connect to workers:

```bash
vagrant ssh worker1
vagrant ssh worker2
```

Run the join command:

```bash
sudo kubeadm join <MASTER-IP>:6443 --token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>
```

---

## Verify the Cluster

From the master node:

```bash
kubectl get nodes
```

Expected output:

```
master     Ready
worker1    Ready
worker2    Ready
```

---

## Learning Objectives

This project demonstrates:

* Infrastructure provisioning with Vagrant
* Kubernetes installation using kubeadm
* Cluster networking setup
* Automated node preparation
* DevOps automation fundamentals

---

## Why This Project?

Instead of installing Kubernetes manually on physical machines, this repository provides:

<ul>
  <li>Reproducible environment</li>
  <li>Local Kubernetes lab</li>
  <li>Beginner-to-intermediate DevOps workflow</li>
  <li>Foundation before moving to Ansible or Terraform automation</li>
</ul> 

---

## Possible Improvements

* Full cluster automation (auto `kubeadm init`)
* Automatic worker join
* Ansible-based provisioning

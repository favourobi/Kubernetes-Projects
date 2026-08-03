# Installing Kubernetes Components (kubeadm)

## Overview

Before a Kubernetes cluster can be created, the K8s components must be installed on every node. This project uses **kubeadm** as the cluster bootstrap tool. In addition to `kubeadm`, the installation also includes `kubelet`, which runs on every node, and `kubectl`, the command-line utility used to interact with the Kubernetes cluster.

This stage was performed on all three virtual machines:

- Control Plane
- Worker Node 1
- Worker Node 2

---

## Why kubeadm?

`kubeadm` is the official Kubernetes tool for bootstrapping clusters. It automates many of the tasks required to create a Kubernetes cluster, including generating certificates, configuring the control plane, and preparing worker nodes to join the cluster.

Although this section focuses on `kubeadm`, two additional components are required:

- **kubelet** – The node agent needed for running workloads and communicating with the Kubernetes API Server.
- **kubectl** – The command-line tool used to manage Kubernetes clusters.

---

## Update the Package Index

Update the package index.

```bash
sudo apt update
```

---

## Install Required Packages

Install the packages required to securely access the K8s package repository.

```bash
sudo apt install -y apt-transport-https ca-certificates curl gpg
```

---

## Add the Kubernetes Repository

Create the keyring directory.

```bash
sudo mkdir -p -m 755 /etc/apt/keyrings
```

Download the Kubernetes repository signing key.

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.36/deb/Release.key \
| sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Add the Kubernetes package repository.

```bash
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.36/deb/ /" \
| sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Update the package index again.

```bash
sudo apt update
```

---

## Install Kubernetes Components

Install the K8s packages (that is kubelet, kubeadm and kubectl).

```bash
sudo apt install -y kubelet kubeadm kubectl
```

---

## Prevent Automatic Package Upgrades

Prevent Kubernetes packages from being upgraded automatically. Kubernetes components were placed on hold using apt-mark hold to prevent 
unintended upgrades through the operating system's package manager. 
Kubernetes upgrades should be performed in a controlled manner using the kubeadm upgrade workflow to maintain version compatibility between cluster components and minimize the risk of instability. 
This approach helps ensure a predictable and reliable cluster lifecycle.

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

This helps maintain version consistency across all cluster nodes.

---

## Enable the kubelet Service

Enable the kubelet service.

```bash
sudo systemctl enable --now kubelet
```

It is normal for `kubelet` to wait for cluster configuration until the control plane is initialized.

---

## Verify the Installation

Verify that each component has been installed successfully.

```bash
kubeadm version
```

```bash
kubelet --version
```

```bash
kubectl version --client
```

Successful execution of these commands confirms that the Kubernetes components have been installed correctly.

---

## Summary

In this section, the Kubernetes software required to create a cluster was installed on all three virtual machines. The official Kubernetes package repository was added, the Kubernetes components were installed, and automatic package upgrades were disabled to ensure version consistency across the cluster.

With the installation complete, the environment is now ready to initialize the Kubernetes control plane using `kubeadm`.

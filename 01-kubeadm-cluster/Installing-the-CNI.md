# Installing the Container Network Interface (CNI)

## Overview

After initializing the Kubernetes control plane and joining the worker nodes, the cluster still lacks Pod networking. Kubernetes requires a Container Network Interface (CNI) plugin to enable communication between Pods running on different nodes.

This project uses **Calico v3.32**, a production-ready CNI plugin that is compatible with Kubernetes v1.36. Calico configures Pod networking, enables inter-node communication, and allows Kubernetes components such as CoreDNS to function correctly.

This stage was performed **only on the Control Plane node**.

---

## Why is a CNI Required?

By default, Kubernetes does not provide a networking implementation.

A CNI plugin is responsible for:

- Assigning IP addresses to Pods.
- Enabling communication between Pods on different nodes.
- Configuring network routing.
- Supporting Kubernetes Services.
- Enabling DNS communication within the cluster.

Without a CNI plugin:

- Nodes remain in the **NotReady** state.
- CoreDNS Pods remain in the **Pending** state.
- Application Pods cannot communicate across the cluster.

---

## Verify the Current Cluster Status

Display the registered nodes.

```bash
kubectl get nodes
```

At this stage, all nodes should display a **NotReady** status.

---

Display the Kubernetes system Pods.

```bash
kubectl get pods -n kube-system
```

Observe that the CoreDNS Pods remain in the **Pending** state.

---

## Install Calico

Deploy Calico v3.32.

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.32.0/manifests/calico.yaml
```

This command creates the resources required for Calico, including Custom Resource Definitions (CRDs), Deployments, DaemonSets, Service Accounts, ConfigMaps, and RBAC resources.

<img width="1563" height="926" alt="image" src="https://github.com/user-attachments/assets/b2f658fb-4bec-44f2-a5c8-278c944d5ac9" />

---

## Verify the Calico Installation

Display the Calico Pods.

```bash
kubectl get pods -n kube-system
```

Initially, some Pods may display `ContainerCreating` or `Init`. Wait until all Pods transition to the **Running** state.

<img width="1012" height="347" alt="image" src="https://github.com/user-attachments/assets/146d216d-e965-4925-b991-d91cae990396" />

---

## Verify the Cluster

Display the registered nodes.

```bash
kubectl get nodes
```

After Calico has initialized successfully, all nodes should transition to the **Ready** state.

<img width="970" height="148" alt="image" src="https://github.com/user-attachments/assets/fb419c31-dfc0-42d4-b251-c53c237e5a70" />

---

Verify the Kubernetes system Pods.

```bash
kubectl get pods -n kube-system
```

The CoreDNS Pods should now be in the **Running** state.

<img width="1013" height="350" alt="image" src="https://github.com/user-attachments/assets/c4684a22-eb7d-4c8f-8714-436ff1db26e9" />

---

Display additional node information.

```bash
kubectl get nodes -o wide
```

This command displays each node's role, internal IP address, operating system, container runtime, and Kubernetes version.

<img width="1910" height="180" alt="image" src="https://github.com/user-attachments/assets/24077b2a-a2e7-4114-a7d7-7d3d94465e39" />

---

## Summary

In this stage, Calico v3.32 was installed as the Container Network Interface (CNI) plugin for the Kubernetes cluster. Calico configured Pod networking across all nodes, enabling communication between Pods and allowing Kubernetes system components to function correctly.

Following the installation, all nodes transitioned from **NotReady** to **Ready**, and the CoreDNS Pods entered the **Running** state, indicating that the cluster networking had been successfully configured.

With networking now operational, the Kubernetes cluster is fully functional and ready for deploying and managing containerized applications.

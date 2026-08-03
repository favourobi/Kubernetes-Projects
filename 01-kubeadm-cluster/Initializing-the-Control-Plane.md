# Initializing the Kubernetes Control Plane

## Overview

After installing the Kubernetes components (`kubeadm`, `kubelet`, and `kubectl`), the next step is to initialize the Kubernetes control plane. This process creates the Kubernetes cluster by configuring the control plane components, generating the required certificates, initializing the `etcd` datastore, and preparing the cluster for worker nodes to join.

This stage was performed **only on the Control Plane node** (`k8s-controlplane`).

---

## Verify the Kubernetes Components

Before initializing the cluster, verify that the Kubernetes components are installed correctly.

```bash
kubeadm version
```

```bash
kubelet --version
```

```bash
kubectl version --client
```

These commands display the installed versions of the Kubernetes components and confirm that the installation completed successfully.

If you did not get hold of the join command, you can get another easily from the control plane node.

Use the command below:
```bash
kubeadm token create --print-join-command
```
This command creates a new bootstrap token (if necessary) and prints the complete command required to join worker nodes to the cluster.

<img width="1891" height="242" alt="image" src="https://github.com/user-attachments/assets/613df957-3dbd-41b6-bc8e-32a8e4a7b99f" />

---

## Initialize the Kubernetes Control Plane

Initialize the cluster using `kubeadm`.

```bash
sudo kubeadm init \
  --apiserver-advertise-address=192.168.56.10 \
  --pod-network-cidr=192.168.0.0/16
```

### Command Parameters

| Parameter | Description |
|-----------|-------------|
| `--apiserver-advertise-address=192.168.56.10` | Specifies the IP address that the Kubernetes API Server advertises to other nodes in the cluster. This is the static IP address assigned to the control plane node. |
| `--pod-network-cidr=192.168.0.0/16` | Defines the IP address range allocated to Pods. This CIDR is compatible with the Calico CNI plugin that will be installed later in the project. |

During initialization, `kubeadm` automatically performs several tasks, including:

- Running preflight checks
- Generating TLS certificates
- Initializing the `etcd` datastore
- Deploying the Kubernetes control plane components
- Creating the cluster configuration
- Generating a bootstrap token
- Creating the worker node join command

The initialization process may take several minutes to complete.

<img width="1307" height="223" alt="image" src="https://github.com/user-attachments/assets/bda49377-8af8-4bfc-a6b3-6ca93a5e4a6c" />

<img width="1522" height="558" alt="image" src="https://github.com/user-attachments/assets/0bb23f0d-fe39-48fa-8365-3cefa58e6352" />


```text
Your Kubernetes control-plane has initialized successfully!
```

---

## Configure kubectl

After the control plane has been initialized, configure `kubectl` to allow the current user to communicate with the Kubernetes API Server.

Create the Kubernetes configuration directory.

```bash
mkdir -p $HOME/.kube
```

Copy the administrator configuration file.

```bash
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

Assign ownership of the configuration file to the current user.

```bash
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

These commands allow the current user to execute Kubernetes administrative commands without requiring elevated privileges.

<img width="1225" height="137" alt="image" src="https://github.com/user-attachments/assets/f08ad6ef-ccb3-4d2c-844d-94025093f388" />

---

## Verify the Control Plane

Verify that the Kubernetes control plane is running.

```bash
kubectl cluster-info
```

A successful response should indicate that the Kubernetes control plane is accessible.

<img width="1462" height="147" alt="image" src="https://github.com/user-attachments/assets/12ea2a11-e39e-4015-a8b8-c4d6b5dd7110" />

---

Display the registered nodes.

```bash
kubectl get nodes
```

At this stage, the control plane node is expected to appear with a **NotReady** status. This is normal because a Container Network Interface (CNI) plugin has not yet been installed.

<img width="1167" height="102" alt="image" src="https://github.com/user-attachments/assets/7313182c-6f5b-4d92-b988-f7dc9e1d41c5" />

---

Display the Kubernetes system Pods.

```bash
kubectl get pods -n kube-system
```

This command lists the Pods running within the `kube-system` namespace, including the control plane components such as:

- `etcd`
- `kube-apiserver`
- `kube-controller-manager`
- `kube-scheduler`
- `kube-proxy`
- `coredns`

Some Pods may not yet be in the `Running` state until a CNI plugin is installed.

<img width="1188" height="216" alt="image" src="https://github.com/user-attachments/assets/7266e0d5-4595-4351-b3bf-14d8de08ae7d" />

---

## Save the Worker Node Join Command

At the end of the initialization process, `kubeadm` generates a command similar to the following:

```bash
kubeadm join 192.168.56.10:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash>
```

Save this command because it will be executed on each worker node during the next stage of the project to securely join them to the Kubernetes cluster.

---

## Summary

In this stage, the Kubernetes control plane was successfully initialized using `kubeadm`. The control plane components were deployed, the Kubernetes administrator configuration was created, and the cluster was verified using `kubectl`.

Although the control plane is now operational, the cluster will remain in the **NotReady** state until a Container Network Interface (CNI) plugin is installed and the worker nodes join the cluster.

The generated `kubeadm join` command will be used in the next stage to add the worker nodes to the Kubernetes cluster.

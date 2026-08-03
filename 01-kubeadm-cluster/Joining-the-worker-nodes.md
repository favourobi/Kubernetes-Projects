# Joining the Worker Nodes

## Overview

After initializing the Kubernetes control plane, the next step is to join the worker nodes to the cluster. This process allows the control plane to manage workloads running on the worker nodes and enables the cluster to scale beyond a single machine.

This stage was performed **only on the two worker nodes**.

---

## Generate the Join Command

If the original join command displayed during `kubeadm init` is unavailable, generate a new one on the control plane.

```bash
kubeadm token create --print-join-command
```

---

## Join Worker Node 1

On **Worker Node 1**, execute the generated join command.

```bash
sudo kubeadm join <control-plane-ip>:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash>
```

During this process, the worker node performs preflight checks, downloads the cluster configuration, and registers itself with the Kubernetes control plane.

<img width="1893" height="473" alt="image" src="https://github.com/user-attachments/assets/f2a154b1-f1bb-4cb4-9c07-f05be5401b01" />

---

## Join Worker Node 2

Repeat the same process on **Worker Node 2** using the same join command.

```bash
sudo kubeadm join <control-plane-ip>:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash>
```

After successful execution, the worker node becomes part of the Kubernetes cluster.

<img width="1880" height="472" alt="image" src="https://github.com/user-attachments/assets/7308ce87-6c6b-4ec4-a91c-85a094ca0ef6" />

---

## Verify the Cluster Nodes

Return to the control plane and verify that all nodes have joined the cluster.

```bash
kubectl get nodes
```

At this stage, all nodes are expected to appear with a **NotReady** status because a Container Network Interface (CNI) plugin has not yet been installed.

<img width="1017" height="122" alt="image" src="https://github.com/user-attachments/assets/7a316c8e-142f-4e0f-99e2-f26a306aff70" />

---

## (Optional) Inspect Worker Node Details

View detailed information about a worker node.

```bash
kubectl describe node k8s-worker1
```

<img width="1913" height="931" alt="image" src="https://github.com/user-attachments/assets/2025dfe8-c4ba-48dc-84f9-73e94690f99f" />

```bash
kubectl describe node k8s-worker2
```

<img width="1886" height="1011" alt="image" src="https://github.com/user-attachments/assets/65434df1-ce89-494b-9a79-40c6dfd680a1" />

These commands display node information such as labels, capacity, conditions, and allocated resources.

---

## Summary

In this stage, both worker nodes were successfully joined to the Kubernetes cluster using the `kubeadm join` command. The control plane now recognizes all nodes, and the cluster is ready for network configuration.

The next stage is to install a Container Network Interface (CNI) plugin, which will enable Pod networking and transition the nodes to the **Ready** state.

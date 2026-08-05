# Troubleshooting

## Overview

During the deployment of the Kubernetes cluster, several issues were encountered. Rather than being obstacles, these issues provided valuable learning opportunities and improved understanding of Kubernetes architecture, Linux networking, and cluster administration.

This section documents each problem, its cause, the troubleshooting process, and the solution that restored the cluster to a healthy state.

---

# 1. kubeadm Preflight Check Failed (IP Forwarding)

## Problem

During the initialization of the Kubernetes control plane, the following error was returned:

```text
[ERROR FileContent--proc-sys-net-ipv4-ip_forward]:
/proc/sys/net/ipv4/ip_forward contents are not set to 1
```

### Cause

Kubernetes requires IPv4 packet forwarding so that Pods on different nodes can communicate through the Container Network Interface (CNI). By default, Ubuntu Server had IP forwarding disabled.

### Solution

Temporarily enable IP forwarding.

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Make the configuration persistent across reboots.

```bash
echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.d/k8s.conf
s]udo sysctl --system
```

### Verification

```bash
cat /proc/sys/net/ipv4/ip_forward
```

Expected output:

```text
1
```


---

# 2. Worker Node Could Not Join the Cluster

## Problem

Executing the join command on a worker node produced the following error:
g
```text
[ERROR IsPrivilegedUser]:
user is not running as root
```

### Cause

The `kubeadm join` command requires administrative privileges. The command was executed as a normal user.

### Solution

Execute the command using `sudo`.

```bash
sudo kubeadm join <control-plane-ip>:6443 \
--token <token> \
--discovery-token-ca-cert-hash sha256:<hash>
```

### Verification

On the control plane:

```bash
kubectl get nodes
```

All worker nodes should appear with a **Ready** status.


---

# 3. CoreDNS Pods Remained Pending

## Problem

After initializing the control plane, the CoreDNS Pods remained in the `Pending` state.

```text
coredns-xxxxx   Pending
```

### Cause

A Container Network Interface (CNI) plugin had not yet been installed. Without a CNI plugin, Kubernetes cannot assign network interfaces to Pods, preventing CoreDNS from starting.

### Solution

Install Calico as the cluster networking solution.

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.32.0/manifests/calico.yaml
```

Calico v3.32 is the version compatible with my version of K8s (1.36.3)

### Verification

```bash
kubectl get pods -n kube-system
```

CoreDNS should eventually transition to the `Running` state.

---

# 4. Calico Pods Were Not Ready Immediately

## Problem

After installing Calico, several Pods displayed readiness probe failures.

Example messages included:

```text
BIRD is not ready

BGP not established

felix is not ready
```

### Cause

Immediately after installation, Calico requires time to initialize networking components, configure BGP peering (when enabled), and establish communication between cluster nodes. During this initialization phase, temporary readiness probe failures are expected.

### Solution

Monitor the Pods until initialization completes.

```bash
kubectl get pods -n kube-system -w
```

### Verification

```bash
kubectl get pods -n kube-system
```

All Calico Pods should eventually reach the `Running` state.

---

# 5. No Resources Found in the calico-system Namespace

## Problem

Running the following command returned no resources:

```bash
kubectl get pods -n calico-system
```

Output:

```text
No resources found in calico-system namespace.
```

### Cause

The official Calico v3.32 manifest deploys its components into the `kube-system` namespace instead of creating a dedicated `calico-system` namespace.

### Solution

View the Pods in the correct namespace.

```bash
kubectl get pods -n kube-system
```

### Verification

Calico components should appear within the `kube-system` namespace.


---

# 6. All Kubernetes Nodes Reported the Same Internal IP Address

## Problem

Running:

```bash
kubectl get nodes -o wide
```

displayed the same Internal IP (`10.0.2.15`) for all nodes.

### Cause

Each virtual machine contained two network interfaces:

- NAT Adapter
- Host-Only Adapter

Kubelet automatically selected the NAT interface rather than the Host-Only interface used for cluster communication.

### Solution

Configure kubelet to advertise the Host-Only interface by editing:

```bash
sudo nano /etc/default/kubelet
```

Example configuration for the control plane:

```text
KUBELET_EXTRA_ARGS="--node-ip=192.168.56.10"
```

Restart kubelet.

```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

Repeat the process for each worker node using its respective Host-Only IP address.

### Verification

```bash
kubectl get nodes -o wide
```

Expected output:

```text
k8s-controlplane   192.168.56.10
k8s-worker1        192.168.56.11
k8s-worker2        192.168.56.12
```


---

# 7. TLS Handshake Timeout

## Problem

Creating a Deployment resulted in the following error:

```text
net/http: TLS handshake timeout
```

### Cause

The Kubernetes API server was temporarily unavailable while the control plane components were stabilizing after configuration changes. This prevented `kubectl` from completing the secure TLS connection.

### Solution

Verify that the API server and kubelet were running correctly.

```bash
kubectl get nodes
```

```bash
sudo systemctl status kubelet
```

```bash
sudo ss -tlnp | grep 6443
```

Once the API server was confirmed to be healthy, retrying the Deployment creation succeeded.

### Verification

```bash
kubectl create deployment nginx-demo --image=nginx
```

Expected output:

```text
deployment.apps/nginx-demo created
```


---

# 8. Copy-and-Paste Did Not Work in Ubuntu Server

## Problem

Copying and pasting text into the Ubuntu Server console inside VirtualBox was unreliable despite enabling bidirectional clipboard sharing.

### Cause

Ubuntu Server does not include a graphical desktop environment. VirtualBox clipboard integration depends on Guest Additions and a graphical session, neither of which are available in a default server installation.

### Solution

Use SSH from Windows Terminal instead of the VirtualBox console.

Example:

```bash
ssh favour@192.168.56.10
```

SSH provides reliable copy-and-paste functionality and simplifies cluster administration.

### Verification

Successfully connect to each virtual machine from Windows Terminal.

The troubleshooting process reinforced several important Kubernetes administration concepts, including Linux networking, kubeadm preflight validation, kubelet configuration, Container Network Interface (CNI) deployment, node registration, and control plane health verification.

By systematically identifying the root causes and applying appropriate fixes, the Kubernetes cluster was successfully deployed as a stable multi-node environment capable of running and managing containerized workloads.

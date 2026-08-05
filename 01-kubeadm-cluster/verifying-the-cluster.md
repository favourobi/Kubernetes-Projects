# Verifying the Kubernetes Cluster

## Overview

After configuring the Kubernetes control plane, joining the worker nodes, and installing the Calico Container Network Interface (CNI), the final step is to verify that the cluster can successfully deploy and manage containerized workloads.

In this stage, an NGINX application is deployed to confirm that the scheduler, networking, and service discovery components of Kubernetes are functioning correctly.

---

## Verify Cluster Health

Confirm that all cluster nodes are in the **Ready** state.

```bash
kubectl get nodes -o wide
```

Verify that all Kubernetes system Pods are running.

```bash
kubectl get pods -n kube-system
```

<img width="1912" height="583" alt="image" src="https://github.com/user-attachments/assets/fcd7a0b9-4578-4afa-89f2-de5635377b23" />

---

## Deploy an NGINX Application

Create a Deployment.

```bash
kubectl create deployment nginx-demo --image=nginx
```

Scale the Deployment to three replicas.

```bash
kubectl scale deployment nginx-demo --replicas=3
```

Verify the Deployment.

```bash
kubectl get deployments
```

<img width="1287" height="315" alt="image" src="https://github.com/user-attachments/assets/070876b8-c0bd-4706-ac48-746dee045b32" />

---

## Verify the Pods

Display all Pods and the nodes on which they are running.

```bash
kubectl get pods -o wide
```

Observe that Kubernetes automatically schedules Pods across the available nodes.

<img width="1635" height="142" alt="image" src="https://github.com/user-attachments/assets/0b17ffb9-1079-4502-aa98-ee8303c7e5c6" />


---

## Inspect the Deployment

Display detailed Deployment information.

```bash
kubectl describe deployment nginx-demo
```

This command displays the Deployment configuration, replica count, rollout strategy, and event history.

<img width="1525" height="812" alt="image" src="https://github.com/user-attachments/assets/abc5609a-de93-4416-9c3e-67d68f1667c4" />


---

## Create a Service

Expose the Deployment using a NodePort Service.

```bash
kubectl expose deployment nginx-demo \
  --type=NodePort \
  --port=80
```

Verify the Service.

```bash
kubectl get svc
```

<img width="1273" height="233" alt="image" src="https://github.com/user-attachments/assets/1ac3e6db-2726-4a83-856b-e9860a6561c4" />


---

## Access the Application

Open a web browser and navigate to:

```
http://192.168.56.10:<NodePort>
```

The default NGINX welcome page should be displayed.

The application can also be accessed through the worker node IP addresses using the same NodePort.

<img width="1042" height="432" alt="image" src="https://github.com/user-attachments/assets/9629a4f2-22a0-40b3-92a7-ad63df1b9dd9" />


---

## Verify Service Endpoints

Display the Service endpoints.

```bash
kubectl get endpoints nginx-demo
```

This confirms that the Service correctly routes traffic to the running Pods.

<img width="1218" height="131" alt="image" src="https://github.com/user-attachments/assets/2901e38c-23d1-43dc-b011-059e826fba94" />

---

## Clean Up

Delete the Service.

```bash
kubectl delete svc nginx-demo
```

Delete the Deployment.

```bash
kubectl delete deployment nginx-demo
```

Verify that the application resources have been removed.

```bash
kubectl get all
```
<img width="1198" height="227" alt="image" src="https://github.com/user-attachments/assets/b5b09ea3-63f4-49da-bd35-07bb53d32c04" />

---

## Summary

In this stage, the Kubernetes cluster was validated by deploying a sample NGINX application. The successful scheduling of Pods, creation of a Service, and accessibility of the application confirmed that the control plane, worker nodes, networking, and service discovery components were functioning correctly.

This verification demonstrates that the multi-node Kubernetes cluster is fully operational and ready for deploying and managing containerized applications.

# Building a Multi-node Kubernetes Cluster Using kubeadm (3 nodes actually, 1 control plane node and 2 worker nodes)

This project documents the manual installation of a Kubernetes cluster using VirtualBox.

# Project Overview

The aim of this project is to document the manual installation and configuration of a multi-node Kubernetes cluster using Oracle VirtualBox and Ubuntu Server virtual machines. I did this project cluster with kubeadm and is made up of one control plane node and two worker nodes.

The primary goal of this project is to show how one can have a start with Open source tools like K8s and gain practical experience with Kubernetes cluster deployment and administration by performing each configuration step manually. Well we can automate this but I chose not rely on automation tools or managed Kubernetes services for now. This, I believe will help beginners gain a deeper insight in every stage of the setup—including virtual machine provisioning, operating system preparation, container runtime installation, Kubernetes component installation, cluster initialization, networking, and worker node integration—is completed manually to develop a deeper understanding of how Kubernetes works.

Throughout this project, each step is documented with explanations, commands, verification procedures, and screenshots. This repository serves as both a personal learning journal and a technical reference for anyone interested in building a Kubernetes cluster from scratch in a local lab environment.

# Project Objectives

The objectives of this project are to:

1. [Build a multi-node Kubernetes cluster consisting of one control plane node and two worker nodes using Oracle VirtualBox] (01-kubeadm-cluster/Lab-Environment.md)
2. Install and configure Kubernetes manually using kubeadm, kubelet, and kubectl.
3. Configure the underlying Ubuntu Server virtual machines to meet Kubernetes prerequisites.
4. Install and configure a container runtime to support Kubernetes workloads.
5. Initialize the Kubernetes control plane and securely join worker nodes to the cluster.
6. Deploy a Container Network Interface (CNI) plugin to enable communication between cluster nodes.
7. Verify the health and functionality of the cluster using Kubernetes administrative commands.
8. Develop practical skills in Kubernetes installation, cluster administration, troubleshooting, and validation.
9. Produce clear, reproducible documentation that can serve as a reference for future Kubernetes projects and learning.

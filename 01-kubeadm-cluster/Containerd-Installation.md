# Installing Containerd

On all VMs, we are going to install containerd (for running containers)

# Installing and Configuring containerd

## Why containerd?

Before Kubernetes can run workloads, each node requires a **container runtime** responsible for creating and managing containers. Kubernetes itself does not run containers directly; instead, it communicates with a container using the **Container Runtime Interface (CRI)**.

For this project, **containerd** was selected as the container runtime because it is lightweight, CNCF-supported, and the recommended runtime for modern Kubernetes deployments using `kubeadm`. It provides the functionality required for Kubernetes to create, start, stop, and manage containers efficiently.

---

## Update the System

Before installing containerd, the package repository was updated to ensure that the latest package information was available.

### Update the package lists

```bash
sudo apt update
```

(Optional) Upgrade installed packages:

```bash
sudo apt upgrade -y
```

Updating the system helps prevent package dependency issues and ensures compatibility with the latest software versions.

<img width="1250" height="180" alt="image" src="https://github.com/user-attachments/assets/3538c5d7-772c-4a13-8c30-6d962c768f4c" />

---

## Install containerd

Install the container runtime using Ubuntu's package manager.

```bash
sudo apt install -y containerd
```

After installation, verify that containerd has been successfully installed.

```bash
containerd --version
```

Example output:

```text
containerd github.com/containerd/containerd ...
```

This confirms that the container runtime has been successfully installed.

<img width="857" height="56" alt="image" src="https://github.com/user-attachments/assets/4d024b31-63c5-4d05-af4d-2aa1f461a738" />

---

## Generate the Default Configuration

Containerd uses a configuration file to define its runtime settings. Generate the default configuration before making any modifications.

First, ensure the configuration directory exists.

```bash
sudo mkdir -p /etc/containerd
```

Generate the default configuration file.

```bash
containerd config default | sudo tee /etc/containerd/config.toml
```

The generated configuration file is stored at:

```text
/etc/containerd/config.toml
```

This file contains the runtime configuration that Kubernetes will use.

<img width="1247" height="497" alt="image" src="https://github.com/user-attachments/assets/98a655a1-6806-49fb-812d-5aa5d056f7f3" />

---

## Configure the systemd Cgroup Driver

By default, containerd uses the `cgroupfs` cgroup driver. However, Kubernetes recommends using the **systemd** cgroup driver on modern Linux distributions because it aligns with Ubuntu's init system and improves resource management consistency.

Open the configuration file.

```bash
sudo nano /etc/containerd/config.toml
```

Locate the following line:

```text
SystemdCgroup = false
```

Modify it to:

```text
SystemdCgroup = true
```

Save the file and exit the editor.

This configuration ensures that both **containerd** and **kubelet** use the same cgroup driver, preventing compatibility issues during cluster initialization.

<img width="957" height="240" alt="image" src="https://github.com/user-attachments/assets/43e2905f-21e0-48d0-92f0-aaf25e4572e0" />

---

## Restart and Enable the Service

Restart containerd to apply the configuration changes.

```bash
sudo systemctl restart containerd
```

Enable the service so that it starts automatically whenever the virtual machine boots.

```bash
sudo systemctl enable containerd
```

> **Note:** Depending on your system, the `enable` command may not display any output if the service is already enabled. This behavior is normal.

Check the service status.

```bash
sudo systemctl status containerd
```

A successful installation should display:

```text
Active: active (running)
```

<img width="1297" height="267" alt="image" src="https://github.com/user-attachments/assets/2c1f992d-20b4-452e-bfc4-327376ed3a76" />

---

## Verify the Installation

Perform the following checks to verify that containerd is functioning correctly.

Check the installed version.

```bash
containerd --version
```

Verify communication with the containerd daemon.

```bash
sudo ctr version
```

Confirm that the service is enabled.

```bash
systemctl is-enabled containerd
```

Expected output:

```text
enabled
```

Finally, verify that the service is actively running.

```bash
sudo systemctl status containerd
```

If the service reports **Active: active (running)**, the installation has been completed successfully.

<img width="1005" height="307" alt="image" src="https://github.com/user-attachments/assets/e7db1670-d58e-49bb-a33c-81b28976a97f" />

---

## Summary

In this section, **containerd** was installed and configured on all three Ubuntu Server virtual machines. A default configuration file was generated, and the **systemd** cgroup driver was enabled to ensure compatibility with Kubernetes. The containerd service was then restarted, enabled to start automatically during system boot, and verified to be running correctly.

With the container runtime successfully configured, the environment is now ready for the installation of the Kubernetes components: **kubeadm**, **kubelet**, and **kubectl**.

We will focus on **kubeadm** for this stage!

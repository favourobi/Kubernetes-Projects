# Download the ISO image

You can get a good image from the official website (https://ubuntu.com/download/server). I used Ubuntu Server 24.04 LTS for this project

In Virtual Box, configure the following minimal settings:
RAM = 2048MiB; 2 processors; Storage: 20GiB

# Control Plane Node

The first VM will be the Control Plane Node.

<img width="716" height="212" alt="image" src="https://github.com/user-attachments/assets/64838c0d-2ae6-4922-a26a-db7f6d635fae" />

Repeat the same process for the Worker Nodes with the same specifications.

In an actual Enterprise environment, you will not do this manually. Again, this setup has 1 control plane node which is not a standard setup.

Standard setups usually have at least 3 control plane nodes for High Availability. In the event that one control plane node goes down, the others take over and no information is lost due to downtime. It also reduces the risk of actual downtimes and ensures better operating conditions of your cluster. 

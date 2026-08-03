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

# Worker Nodes
## Node 1
<img width="697" height="217" alt="image" src="https://github.com/user-attachments/assets/1d7e4a40-ae55-4b4f-ae07-7486cd2517d8" />

## Node 2
<img width="822" height="212" alt="image" src="https://github.com/user-attachments/assets/e2a1d8f2-4089-46c9-92eb-ce60d6b6ee14" />

Now our initial setup has been completed and finalized.

Do not forget to update your machine. Enable and Start ssh too.

Make sure swap is disabled on all VMs and disable firewall for now (In the future, we would enable it).

Make sure that curl, git, wget and nano are installed on all the VMs too. 

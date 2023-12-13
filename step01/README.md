# Instructions

- Install Virtual Box
- Download Debian image and use it to install each virtual machine
- In order to install the cluster with Virtual Box, follow the link:
    https://medium.com/@mojabi.rafi/create-a-kubernetes-cluster-using-virtualbox-and-without-vagrant-90a14d791617
- When configuring the network for each node, make sure to configure the static IP using the following:
  - Open file `/etc/network/interfaces`
  - One of the network interfaces should be DHCP. Use the other one e.g. enp0s8 and add the following
    ```
    auto enp0s8 
    iface enp0s8 inet static
    address 192.168.60.11
    network 192.168.60.0
    netmask 255.255.255.0
    ```
  - Save the file and restart the network: `systemctl restart networking`
- Once the nodes are up and running, install `openssh-server`
  - Steps:
    - `apt-get update`
    - If there are errors in the repositories, modify file `/etc/apt/sources.list` and rerun `apt-get update`
    - `apt-get install openssh-server`
    - Make sure ssh is installed correctly by running `systemctl status ssh`
  This will allow to ssh into the VMs without using VirtualBox terminals.
- Once the infrastructure is ready, as especified in the instructions, we need to install kubeadm and kubelet. The link provides instructions to install an old version from Kubernetes Xenial repo but as of the writting of these instructions, it is recomended to install from the community supported repo for Debian like systems. Follow the link https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl for instructions


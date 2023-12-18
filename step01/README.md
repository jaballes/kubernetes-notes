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
  - In this instalation, we do the following on all nodes:
    ```
      apt-get install -y apt-transport-https ca-certificates curl gpg
      mkdir /etc/apt/keyrings
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | tee /etc/apt/sources.list.d/kubernetes.list
      apt-get update
      apt-get install -y kubelet kubeadm kubectl
      apt-mark hold kubelet kubeadm kubectl
    ```
- Also, to install docker on all the nodes, follow instructions here : https://docs.docker.com/engine/install/debian/
  - In this installation, we perform:
    ```
    apt-get update
    apt-get install ca-certificates curl gnupg
    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    chmod a+r /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |tee /etc/apt/sources.list.d/docker.list > /dev/null
    apt-get update
    apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
- After Docker is installed, the guide recommends changing docker cgroup, given that initially it will not be systemd
  ```
  docker info | grep -i cgroup
  cat <<EOF | tee /etc/docker/daemon.json
  { "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {"max-size": "100m"},
    "storage-driver": "overlay2"
  }
  EOF
  systemctl restart docker
  systemctl status docker
  docker info | grep -i cgroup
  ```


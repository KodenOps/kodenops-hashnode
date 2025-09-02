---
title: "Kubernetes: Getting Started"
seoTitle: "Kubernetes: Getting Started"
seoDescription: "In this article, we will be setting up our Kubernetes environment and create our first Pod. "
datePublished: Tue Sep 02 2025 09:52:39 GMT+0000 (Coordinated Universal Time)
cuid: cmf2ddvjd000602jxa5uo8xdv
slug: kubernetes-getting-started
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ZfVyuV8l7WU/upload/9024acfdc40f93c541eb4265147e5363.jpeg
tags: openshift, docker, kubernetes, devops, containers, k8s

---

In case you missed the previous articles in this Kubernetes Series, you can start from Here: [Kubernetes For Beginners](https://kodenops.hashnode.dev/series/cka-for-beginners)

## Objective

In this article, we will be setting up our Kubernetes environment and create our first Pod. At the end of this article, you should be able to do the following

* Create EC2 instances for both Worker Nodes and Control Plane
    
* Setup Kubernetes, CRIO, Kubeadm and Kubelet
    
* Connect Worker Node To the Control Plane
    
* Have a fully functioning Kubernetes cluster in your VM
    

## Requirements

To be able to fully grasp and follow with understanding, the following will be very important to know:

* Linux Basic
    
* Vim editor or any command line editor (Nano)
    
* AWS EC2 or any cloud platform Virtual Machine
    

## Creating Our Virtual Machines

In this section, we will be creating our VMs where our Kubernetes environment will be setup. The assumption here is that you already have an AWS account. If you are using any other cloud provider, just create three different linux VMs. I will be using Ubuntu (Because I love Ubuntu - not any special reason) you can use any flavour you like. Just know the package manager to use if i am using “APT and APT-GET”.

This is not an AWS tutorial so create the damn thing yourself. Ok, just kidding. Here is my setup

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756755452805/3d474dfc-aa28-4cdd-9677-f72679675d5c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756755459934/04cbfaa5-3856-4832-b3ed-74d7fa6d7976.png align="center")

Leave the rest as default. You Will need to create a new key pair if you don’t have one before

## Setting up Host

The next step is to setup the hosts.ini file in the master node so that it can connect with the rest of the machines seamlessly using a name rather than IP. With this, you can refer to worker node 1 as Node1 instead of worrying about its IP address.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756755929588/0cd6015b-9cec-404f-8e0a-440acc9dcbbb.png align="center")

Here is currently my machines. I have named them accordingly. Note: I didn’t use any criteria in naming them. I just call the first one master and the remaining one node1 and node2. The arrangement is at your discretion.

1. ssh into the master node edit the ./hosts file located at /etc
    
    ```powershell
    sudo vim /etc/hosts
    ```
    
2. Leave all the content of the ./hosts as it is.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756756648485/3995b149-b71f-44e2-9185-ff913768cb90.png align="left")
    
3. Edit the file by inputing your machines IPs and a dedicated hostname for each machines
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756757252054/9b542ca2-c01c-43e7-97af-1e35ea3a7487.png align="left")
    
4. Save the file by pressing ESC button first, then :wq (normal vim shii)
    
5. Now we can refer to each of the machine using the assigned Hostname rather than the IP address.
    

## Installing Kubernetes Component

We will be using kubeadm as our bootstrapper here. It is the production grade bootstrapper that helps us setup our kubernetes cluster. There are other light-weight tools that does the same job but in practise environment and not in production. This includes:

* Kind: Runs Kubernetes clusters entirely in Docker containers. Local testing (especially for CI pipelines), simulating multi-node clusters quickly.
    
* Minikube: Runs Kubernetes locally in a VM or container (like VirtualBox, Docker). Majorly for learning purpose.
    

1. Navigate to your AWS EC2 console, Pick any machine and scroll down to the bottom. Under Click security, then click the security group link (take note of it before clicking)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756767216411/dbd79120-4b61-41e8-aba9-6f342f4a87d8.png align="center")
    
2. edit the inbound for your security group. If you create your machines together like I did, all the machines will have same security group. Edit the inbound rules like this
    
    * In the security group, click **Inbound Rules → Edit inbound rules**.
        
    * Add a new rule:
        
        * **Type:** All traffic
            
        * **Protocol:** All
            
        * **Port Range:** All
            
        * **Source:** Custom → *type the first few strings of your security group and select it from the drop-down*
            
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756767332140/374a1954-55e4-4b79-9f8b-95fe10e00a7f.png align="center")
        
3. Now your machine can connect with one another. To test this, ping the worker IPs from the control plane server and vice versa
    
4. ssh into each machines (don’t forget to reference your key-pair .pem file) and run this command to install kubelet kubeadm kubectl and containerd. To make it fast, create a `./run-kube.sh` file and add execution permission to the file `sudo chmod +x run-kube.sh`. Then run the file using `./run-kube.sh`
    
    ```bash
    # Add the package to your ubuntu registory
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl gpg
    
    # Add GPG key
    sudo mkdir -p /etc/apt/keyrings
    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    
    # Add Kubernetes repo
    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    
    # Install components
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm containerd kubectl
    
    # Enable kubelet
    sudo systemctl enable kubelet containerd --now
    ```
    

Now let’s initiate the control-plane using `Kubeadm`.

> Because we are using small VM with a little cpu and memory, we will need to bypass some errors temporarily. In real production environment setup, you dont need to bypass anything

```bash

# For this project - we bypass because we are using small vm
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=NumCPU,Mem,FileContent--proc-sys-net-ipv4-ip_forward

# In real production, this is what you'll run. Obviously without your preferred IP range
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

This will setup this control-plane for you. Check the ending of the output for these commands. Run them one after the other on the control plane server

```bash
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

1. Now you need to connect each worker nodes to the control plane. Check the end of the outputs from the `kubeadm init` command. You will see something similar to this
    
    ```bash
    kubeadm join 172.31.27.200:6443 --token em5tj4.1bheaioxojrssx4p \
    	--discovery-token-ca-cert-hash sha256:a537ce5282110e75xxxxxxx
    ```
    
    run this command on each of the worker node server using sudo
    
2. After this installation, go to the Master Node and run this command to see all the nodes in your cluster
    
    ```bash
    kubectl get nodes
    ```
    
    You should see something like this
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756768117509/969e4b11-d870-4e48-9f1b-3beb851bb2e8.png align="center")
    
    8. Ignore the NotReady for now. This is because we have not added the Network Interface to it. For this project, we will be using Calico as our network interace. To install this into our cluster, run this command on the master node.
        
        ```bash
        kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
        ```
        
3. Wait for few seconds and run `kubectl get nodes` again. This time, you should see this
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756768466896/318230fc-2fef-4f02-9375-617271764b86.png align="center")
    
    9. Congratulation, you have setup your Kubernetes cluster
        

In the subsequent articles, we will be diving into the actual Kubernetes commands to create, delete and manage pod throughout its lifecycle
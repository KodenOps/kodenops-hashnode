---
title: "Kubernetes: Architecture Explained"
seoTitle: "KUBERNETES ARCHITECTURE EXPLAINED AND SIMPLIFIED"
seoDescription: "This article simplifies the entire kubernetes achitecture in a way that you will never forget using relatable concept to talk about each component"
datePublished: Mon Sep 01 2025 18:08:19 GMT+0000 (Coordinated Universal Time)
cuid: cmf1fnfq3000b02kydbc81ms1
slug: kubernetes-architecture-explained
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/9cXMJHaViTM/upload/5d77ff43ec84a740c314ca5c1ab2a568.jpeg
tags: docker, kubernetes, devops, containerization, infrastructure-as-code, pods

---

In the previous article, we discuss the problem kubernetes solves. In case you miss it, you can catch-up [Here: Kubernetes - Introduction](https://kodenops.hashnode.dev/kuber-what)

With the introduction out of the line, let’s discuss the Kubernetes environment

# Kubernetes Architecture

Instead of cramming the architecture, we will take this in the most relatable approach you can ever imagine such that you won’t need to struggle with remembering the architecture.

One thing that is constant with any management tools is that there will be a part that does the entire work and there will be another part that oversees the workers’ activities.

Just like at work. We have the bottom grades that actually do the dirty work (if I can put it that way). And then we have another set of people who have reached a grade level that makes them supervisors. We call these people management or management committee (MANCO for short).

This same thing applies with Kubernetes. We have these two categories also.

## Control Plane vs Worker Nodes

![What Are Nodes in Kubernetes? Master & Worker Nodes Explained](https://zesty.co/wp-content/uploads/2024/10/k8s-nodes-master-node_worker-node.png align="left")

Assuming you need to create an Apache server to run your application frontendService. With containerization you will need to just run:

```powershell
docker run -d --name my-nginx -p 8080:80 nginx
```

This will create a container where your NGINX will be running normally.

But with Kubernetes the flow is different.

There must be “management” discussion before your NGINX can be created.

## **Master Node**

This is where all the management discussions happen. When the engineer sends in the request to create a new NGINX server on the infrastructure, the master node will do the following before it creates the server:

* Receive the request
    
* Check if there are resources for the new request
    
* Check if there are exactly the same requests currently running
    
* Check which of the worker nodes can handle/run your request
    
* Assign the request to the worker node
    
* Give a response (treated/failed) back to the requester
    

The master node is not where your application actually runs. Rather, it determines if your application should run and which node it should run on.

Just like in our corporate work, management is not just a single individual. They are made up of different experts at the managerial level. They are knowledgeable in the nooks and crannies of all the operations at all levels, and they manage everyone under them to perform those tasks effectively. They are always present at every job interview for new hires under them, and they determine if they want them.

> New Word: PODS
> 
> A **Pod** is the smallest deployable unit. A pod represents **one or more containers that share the same network namespace, storage, and lifecycle**. In Docker and other containerization, where your application runs is called Container but in Kubernetes, your application runs as a Pod

Same way for master nodes. The master node is comprised of the following:

### **API Server:**

This can be likened to the secretary of our boss. The API Server is like the reception desk of the Kubernetes management floor. Any request, whether it’s deploying a new app, scaling up pods, or checking the system’s health must go through the API Server first. It makes sure every request is valid, logged, and sent to the right decision-maker inside the control plane. Without it, the office might still exist, but no one would know where to go or what to do.

> Analogy To Drive Point
> 
> * **What happens:** You submit your request (`kubectl apply -f pod.yaml`).
>     
> * **Analogy:** The Secretary desk receives your job application form and log it into the record.
>     
> * **Reality:** The API Server validates your request and records it in `etcd`.
>     

### ETCD

Ok, this is not the English Alphabet recitation. This can be likened to the excel sheet , sharepoint folders or any tools used by the secretary to keep records of everything going on in the office. Every decision, employee record, or policy is stored here.

The etcd stores the cluster’s configuration and state as a distributed key-value store. This is the single source of truth for the cluster as it holds information about nodes, pods, secrets, configs, and every other object in Kubernetes.

> Analogy to Drive Point
> 
> * **What happens:** The details of your request are stored.
>     
> * **Analogy:** Request files from the secretary are stored here.
>     
> * **Reality:** etcd becomes the single source of truth for your Pod specification.
>     

When you apply a manifest (e.g. kubectl apply -f pod.yml), the following specifications are written into the etcd:

* Pod name, namespace, labels
    
* Container images, environment variables, ports
    
* Resource requests/limits (CPU, memory)
    
* Volumes, secrets, configs
    
* Any metadata or annotations
    

### Scheduler

This is one that assign tasks to workers. This reminds me of secondary school when the Labour prefect will bring everybody out during sanitation day. He is in charge of apportioning field dimensions to everyone to cut. Oh, yes - i went to public school and we are the one in charge of cutting the grasses in the school and the school farm management is also on us. So the Labour Prefect will measure the field for everyone of us to cut based on his perception of strength (And also in retaliation for a bad conduct to him at times). This is the same for kubernetes schedulers also.

> **Analogy:** The Scheduler checks node resources, policies, taints/tolerations, and assigns the Pod to a node — but doesn’t actually create it yet.

### Controller Manager

This is the line manager for the workers. He ensure the task get done and keeps running. Same way Controller manager ensures the Pod actually gets created and keeps it running. The Line Manager makes sure the new employee shows up, has a desk, and stays on the job. If they quit (Pod crashes), a replacement is automatically hired. This is exactly the same thing the CM does in managing the kubernetes worker nodes especially the pods itself.

> **Analogy:** The Controller Manager notices that the Pod assigned by the Scheduler isn’t running yet, so it tells the kubelet on the selected node to start it and keeps monitoring to maintain the desired state.

## Worker Nodes

If you are preparing to write the CKA exam just to be able to work with Kubernetes at work, then this might be where you fall in figuratively. This is where the actual work happens. This is where the application is running as Pods. Just like the junior staff at work. They are the one writing the codes, setting up the environment, deploying, etc. While the Control Plane is the management who give directions and administrative guidance to get a task done.

As you’ve know, even at the lower grades, there are also roles too. The leadership at this level is Team Lead. But the leadership doesn’t mean they won’t ssh into servers and get things done.

The components of Worker Nodes includes:

### Kubelet

This is the Team lead of the particular Pod. It is the only component of the Worker Node that speaks to the Control plane. It is in charge of actually starting and keeping the container running in the pod base on the Pod Specification defined in the manifest. And in returns, report the node and Pod status back to the Control plane (Control Manager)

### Kube-Proxy

This is in-charge of the networking rules and communication among worker nodes. It is involved in assigning IPs based on the IPtable specifications.

### Container Runtime

Even though this is kuernetes, under the hood - what we have is still a container (technically). So, this is where the container runtime comes in. This is exactly what defines the exact container application that powers your pod container. It could be Docker (Legacy), Containerd, CRI-O, or any runtime implementing the Kubernetes CRI (Container Runtime Interface).

![The architecture of Kubernetes - ScaleUp Technologies](https://www.scaleuptech.com/de/wp-content/uploads/2019/03/kubernetes_archiketktur_blog.jpg align="left")

> Image ref: [ScaleUp Tecnologies](https://www.scaleuptech.com/en/blog/kubernetes-architecture/)

This wraps up the second Part of our Kubernetes Journey. In the next article, we will be discussing setting up the environment and using Kubernetes to create and manage Pods.
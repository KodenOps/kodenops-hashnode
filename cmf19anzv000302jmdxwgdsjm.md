---
title: "Kuber - What?"
seoTitle: "Introduction to kubernetes"
seoDescription: "This article highlight the container problems kubernetes solves flawlessly. From monoliths to microservices; from virtual machines to containers."
datePublished: Mon Sep 01 2025 15:10:25 GMT+0000 (Coordinated Universal Time)
cuid: cmf19anzv000302jmdxwgdsjm
slug: kuber-what
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ZfVyuV8l7WU/upload/a821bddeed7ac96c5eb42b1310019c5a.jpeg
tags: virtual-machine, kubernetes, vm, containers, virtualization, containerization, ckad, cka

---

So, you want to build the next big thing.

You’ve written your code. Explore all the clean code strategies and finally you’ve hit the point where you say, let’s GO LIVE!

Now brings the question, HOW?

Obviously, users won’t access your product via your developer’s laptop. And you won’t send them https://localhost:3000 to access (you get the joke).

So, it brings us to the question about HOW ARE YOU GOING TO MAKE YOUR APPLICATION ACCESSIBLE TO YOUR users.

For you to make your application accessible, you need to deploy it on a server; get the IP and then use DNS to map the IP address to a domain name. Then you can ask your friend to go and check your application out on https://mybigproject.com.

The deployment could be as easy as just uploading your html files on a server like NGINX/HTTPD. That is if it’s a single, simple page website.

What happen if the project is complex? Especially if you’ll need to manage each features individually without deploying the entire application everytime you make a tiny change on just a single component like the checkout button colour. It is totally outrageous if for every changes you make, you will need to redeploy the entire application. If this is what you do at work, you might need to flog your entire DevOps team. In fact, your entire IT department needs the flogging.

### Microservice

This is the new shining stuff in the IT space. And the proble it addresses is simple and straight-forward.

Instead of bundling your entire application as a box of codes, why not separate them in terms of functions. Such that each core function exist independently. Then these services can then communicate independently with one another to perform the collective function of your application logic.

E.g. Instead of having your ecommerce applications tightly coupled (where everything is together and deployed together), with microservice, you can separate each functions into services.

For example:

* cartService: To handle your cart logic
    
* checkoutService: To handle the checkout and payment logic
    
* EmailService: to handle sending email to users about their purchase or any other reasons to send mail
    
* etc.
    

This way, you have broken down your application and shifted from the tightly coupled format you have earlier. With this new breakdown, you can make modification to the emailService (maybe change the provider from Yahoo to Google - just saying) and you will only need to deploy the emailService. Not the entire Application.

Sweet right?

You might be wondering how the deployment will go. Will I have multiple servers with Apache HTTPD/NGINX and deploy my html on each of the servers for the individual services?

Hell no!

Microservices are best run as containers.

### What are Container?

Before i explain what container is, first you’ll need to know what virtualization is all about. [Read Here](https://kodenops.hashnode.dev/this-thing-called-virtualization)

Containerization took the virtualization to the next level. In virtualization, the hypervisor (type 1 or two) abstract the host hardware while it builds multiple guest operating systems as virtual environment on the hardware. This way, you can fully utilize your hardware server to run multiple services on different operating systems as needed.

One limitation of virtualization is that it ships all the operating system data into the guest environment which makes it bloated. Also, to start a virtual machine (the guest OS you created on the physical server), it takes a very long time to boot up and configured just to list few downsides.

### How containers fixed Virtual Machine Limitations

As mentioned earlier, virtual machines has its limitiations such as:

* Heavy resource usage: since each vm has the OS files bundled in it before even thinking of our application file we want to deploy on it. So, it is kinda bloated
    
* slow start-up time: Obviously, the guest OS will need to start first before you can even run your application. This makes the startup takes a long time. It’s relatively slow to spin up new vm when one vm has issues.
    
* **Inconsistent environments** – Even with images, subtle differences in OS configuration can cause “works on my machine” problems.
    
* **Inefficient scaling** – Scaling up requires provisioning full VMs, which is slower and more resource-intensive. So, this means, you will have ubuntu-1 where checkoutService-1 and you will have another ubuntu-2 where checkoutService-2 (a replica) will be. We will be duplicating the bloated OS files on all replicas
    

With containers, you don’t need to worry about the operating system at all. This is because containers unlike VMs only package the binaries needed to run your application. Every other files are ignored. A container packages only the application and its dependencies (libraries, binaries) but uses the host OS kernel directly. Since they don’t include an entire OS, container images are smaller and faster to transfer or spin up. You can start containers in seconds just the way you will start a linux service/process rather than booting up a OS as seen in VMs.

## So, why Kubernetes or should I say Kuber-Why?

Believe me when i say chaos could erupt if a process is too simple. People will create a lot of things using that process. Afterall it is simple and fast. That is exactly what happen with containerization. With one container image, you can create billions of containers for that particular image (multiple instances of the service bundled in the image). With the numerous containers being created one problem start showing up. This is HOW TO MANAGE ALL OF THESE CONTAINERS.

An organization can have more than 300 containers. And with high availability and multi-nodes strategies, this can increase to thousands of containers. How can organizations then manage these containers effectively.

Guess who came in to save the day?

\*\*\*imagine superman landing\*\*\*

KUBERNETES!!!!!

Kubernetes is a container orchestration tool. And by orchestration i mean a tool that is capable of doing the following to a container:

* **Automated Deployment** – Launches containers across nodes without manual intervention.
    
* **Service Discovery and Networking** – Assigns internal IPs, DNS names, and load-balances traffic between containers.
    
* **Load Balancing** – Distributes requests evenly across multiple replicas of your containers.
    
* **Scaling (Up and Down)** – Automatically adjusts the number of running containers based on demand.
    
* **Self-Healing** – Restarts containers that crash, replaces failed ones, and reschedules them on healthy nodes.
    
* **Automated Rollouts and Rollbacks** – Gradually updates containers to a new version and reverts if something breaks.
    
* **Resource Management** – Controls how much CPU and memory each container can use.
    
* **Secret and Configuration Management** – Stores sensitive data (passwords, keys) and app settings separately from container images.
    
* **Storage Orchestration** – Automatically mounts and manages persistent storage volumes for stateful apps.
    
* **Monitoring and Logging Integration** – Exposes metrics and integrates with tools to track container health and performance.
    
* **Multi-node Scheduling** – Decides where containers should run based on resource availability and policies.
    
* **Service Mesh Support** – Works with tools to manage secure communication between services.
    

So now, you have someone like a project manager that manages all your containers regarless of their numbers. Also If containers are like individual band members, Kubernetes is the conductor making sure they play in harmony.
---
title: "This Thing Called Virtualization"
seoTitle: "VIRTUALIZATION"
seoDescription: "A simplified explanation of how virtualization pave the way for effective server utilization to minimally reduce over-provisioning and under-utilization"
datePublished: Sat Aug 02 2025 16:24:25 GMT+0000 (Coordinated Universal Time)
cuid: cmdugqaa5000h02l5faqqf2ae
slug: this-thing-called-virtualization
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/PSpf_XgOM5w/upload/90073cad10df357c21c56f09c5deae2e.jpeg
tags: technology, system-design, sre, virtualization

---

### Introduction

If you’ve ever been with these Tech guys while they discuss among themselves, you must have heard them rant about SERVERS with so much passions. Either from them saying “most problems are solved by restarting the server” to talking about Server crash. Ooops! Those guys and big tech grammars! Hian.

This shows how important servers are in the normal functioning of technology. This is because the server houses the applications, websites, software, tools, etc. It is like a central library that stores information and responds to requests. Maybe a little explanation about Server provisioning will help in understanding the need for virtualization which is where I am heading to.

So, mid to big technology inclined company have to house their information and applications on servers. This means if they have an application called JoinX, this application will sit on the particular server and all the interactions involved for the smooth running of the application will be within the server. But two major problem about this is **Over-provisioning** and **underutilization.**

Due to the fact that the organization do not want to have a capacity issue for JoinX application as the utilization scale up, they tend to over provision the capacity. For instance, if the normal capacity needed for the smooth running of the application is 30GB of storage (just saying), they tend to create more room for scalability. They could get a server with storage of 200GB. This is expensive and in situation where they have multiple applications. That means they will get another over-provisioned servers for all the applications that might not be fully utilized. The remaining space will just be siting idly while a need for another server will mean getting a brand new server rather than utilizing the available ones.

> “Saving for the raining days”, abi how do they always say it?

You might be asking, why can’t they put multiple applications on one server. After all, there are enough space on the server.

Good question to be honest. To answer this question, I will point our attention to various operating systems on which some of our applications runs. Let say a server is running on Linux and this is the server on which JoinX is hosted on. Now, we have another application called JoinZ that requires a Windows environment to run some of the services needed for full functionalities. This means there is a compatibility issue already. We will need to look for a window server for this new application.

So, to solve this issue in the tech space, there is an introduction of what is called Virtualization.

Just to save us from the technical jargons. Virtualization is just a means of virtually partition this our physical server. (Just a water-down explanation). So, it’s like partitioning a large event hall into smaller rooms which will be occupied by different tenants. Just the same way Lagos landlords will convert a big sitting room into two mini-flats. (sobs quietly)

In this way, we can use one server to run numerous application regardless of their required environment (since each application will be housed in a partitioned environment). So, one server could host both Windows, Linux and any other environments independently.

Now with the installation of a particular application on the server, it can open different branches on which you can configure an entirely different environment to run other applications rather than leaving the excess space redundant on the server.

> These magic software is called HYPERVISOR.

Sounds cool?

### Categories of Hypervisor

There are two categories of Hypervisor depending on where you install them on the server.

There is a category of Hypervisor that you install directly on the server just the way you will install window 11 on a laptop. These categories are called Type 1 or Bare-metal hypervisor.

There is another category of Hypervisor that you can install the same way you install other software on your computer. They are install directly on the Host operating system the same way you would have imagined software to be installed on either windows or Linux computer.

![](https://cdn-images-1.medium.com/max/1000/0*L_XlB5XOdKXCLJoE.png align="left")

Image showing the two types of Hypervisor

with this hypervisor, you can then create different environments with separate Operating system on this server.

* **For type 1**: The server itself doesn’t have a base operating system. Rather the Hypervisor makes each environments created on this server to feel kinda like they are the main Operating system on the server. For instance, you know how some people buy the individual flats in a complete building in Lekki. Making them a joint owner of the entire block of apartment. This is the same way these virtualized environment are. They are mainly used for production and won’t allow you to use the server for any other thing
    
* **For Type 2**: This kind of approach is mostly used for learning rather than putting production environment on it. The environment is siting virtually on a Host OS. Any issue to the host OS will affect the normal functioning of the Guest OS. The host OS here manages the file system, resources allocation, hardware management, security, user interface, to mention a few. An example is Oracle VirtualBox which you can install on your laptop to create as numerous VMs as your laptop infrastructure can accommodates.
    

Now, with virtualization we can utilize our physical server optimally by running multiple applications on it. And the funny and important part of this virtualization is that an issue on one Virtual machine does not affect either other VM on the same server or the main server housing the VMs. This is because they are in isolation with one another. To prevent loss of data on these VMs, regular snapshot (same as backup) are taken which is just like the state of the VM at that particular time.

Now, you’ve learnt small thing about servers. So, when next you sit with those tech guys discussing about their servers, you can just chip in “Is your server running as VMs? or Didn’t you take a snapshot for the VM before crashing?”. After this, step back a bit and watch the amazement on their faces. At this point, take a sip of your cold drink and relax because you are now a full member of the server-discussing tech gurus.
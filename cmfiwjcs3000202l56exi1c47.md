---
title: "Working With Your First Pod"
datePublished: Sat Sep 13 2025 23:33:07 GMT+0000 (Coordinated Universal Time)
cuid: cmfiwjcs3000202l56exi1c47
slug: working-with-your-first-pod
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/YniQ5vouxqg/upload/a2b38438cf69033eed54d7a90b900678.jpeg
tags: microservices, kubernetes, devops, cncf, cka, kubernetes-container

---

We’ve discuss how to setup your kubernetes environment both in production and locally on your laptop. We will be taking this further into actually working with Kubernetes. In this article, we will be working with Pods for the first time. At the end of this article, you’ll be able to perform confortably the following:

* Create Pods (Imperatively and Declaratively)
    
* Check Pod current state
    
* Working inside a Pod
    
* Perform basic troubleshooting for Pods
    
* Deleting Pod
    

So, Let’s dive In!

## Creating Pods

There are two distinct ways to create pods in kubernetes. These are the Imperative Approach and Declarative Approach.

* Imperative Approach: This involves creating Pods using command without explicitly defining the configuration for the pod. Here, it looks more like the user is saying “Create a pod of this type for me”. The system then create a pod using its descretion to handles the rest of the needed configuration. Whatever specification you didn’t explicitely add to the command, the system will create the pod using the default values for the specification. Let’s look at an example by creating a pod for nginx. We will call the pod name `nginx-pod`
    
    ```bash
    # The syntax for the imperative approach
    Kubectl run <pod name> --image=<image name>:<image tag>
    
    # example
    kubeclt run nginx-pod --image=nginx:latest
    ```
    
    In the example above, we only provided image name for the system to use in creating our new pod. For other details needed for the pod will be handled by the system and the values for these specifications will be set to the default values.
    
* Declarative Approach: This is when you want to be in control of the pod creation. In this method, you create a configuration file to specify all the necessary details you want for your pod. Rather than leaving the specifications to the system to handle, the user will explicitly define them using the YAML file. The configuration file format will be explained later. The configuration is then applied to create the pod using the command below.
    
    ```bash
    # Create your pod / apply changes to the pod
    
    ## SYNTAX
    kubectl apply -f <Configuration file name>
    
    ## USAGE
    kubectl apply -f my-nginx-conf.yaml
    ```
    
    This command will create a new pod with the specification defined in the configuration file. If the Pod already exists, Kubernetes will compare the live state with the configuration file and only apply the differences.If nothing has changed in the configuration, nothing will happen to pod. It will remain as it is. This phenomenon is call IDEMPOTENCY. This means that applying the same configuration multiple times will always produce the same result.
    

### Understanding The Kubernetes YAML syntax

It is crucial to discuss the syntax for the kubernetes yaml file because it will be the basis on which numerous Pods will be created in production.

### **<mark>The AKMS</mark>**

This is the acronym I coined for the basic skeleton of the kubernetes configuration.

```bash
apiVersion: xxx
kind: xxx
metadata: xxxx
spec: xxxx
```

**<mark>A = apiVersion:</mark>** This is version on which the resources is running. Each resources are classified into various groups called APIGroup. This is a logical group meant to separate the resources based on functionality.

Example of these APIGroup (logical groups) include:

* `apps` → for workloads like Deployments, StatefulSets, DaemonSets
    
* `batch` → for Jobs and CronJobs
    
* [`networking.k8s.io`](http://networking.k8s.io) → for networking resources like Ingress
    

To specify the apiVersion, you will have to define APIGroup/version

```bash
apiVersion: apps/v1   # <APIGROUP>/<VERSION> 
kind: xxx
metadata: xxxx
spec: xxxx
```

Another type of APIGroup is the Core Group. Core group as the name implies, it’s for resources that makes the core of kubernetes. They are the fundamental building block for kubernetes cluster.

They comprises:

* **Pod**
    
* **Service**
    
* **ConfigMap**
    
* **Secret**
    
* **Namespace**
    
* **PersistentVolume (PV)**
    
* **PersistentVolumeClaim (PVC)**
    
* **ServiceAccount**
    

These groups uses `v1` as their apiVersion.

```bash
apiVersion: v1   # <For CORE Group> 
kind: xxx
metadata: xxxx
spec: xxxx
```

<mark>K = Kind:</mark> This is used to define the type of resources you want to create. This is case-sensitive and must match what the kubernetes is expecting. The resources may include: Pods, Deployment, ReplicaSet, DaemonSet, etc. It’s just the name of the resources you want to create with the configuration file

```bash
apiVersion: v1   # <For CORE Group> 
kind: Pod  # <Pod is part of core group resources so we are using v1 with it. Note the case also>
metadata: xxxx
spec: xxxx
```

```bash
apiVersion: apps/v1   # <For Apps Group> 
kind: Deployment  # <Deployment is part of the apps APIGroup>
metadata: xxxx
spec: xxxx
```

* `apiVersion` = *which API version to use*.
    
* `kind` = *what type of resource you are defining*.
    

<mark>M = Metadata:</mark> This helps in adding metadata information to the resources we are creating. Kubernetes uses these information to organize, track and manage the resources. Metadata gives us the opprtunity to add the uniqueness required by kubernetes for our resources. All resources must be unique (no two resources must bear same name kinda). With metadata, you can add name for the resources, labels for identification and filtering, etc.

The following can be added to the metadata

* name: to add a unique name to your resources
    
* namespace: for separating environment
    
* Labels: Key/value pairs used to group and select resources
    
* annotation: for storing non-identifyable details about the resources (e.g. build version, owner, documentation link, etc)
    

```bash
apiVersion: apps/v1   
kind: Deployment  
metadata: 
  name: my-nginx-pod
  namespace: prod  # You will need to create this namespace before attaching it here.
  labels:
    apptype: web
    tier: frontend
spec: xxxx
```

* `name` → unique ID
    
* `namespace` → logical grouping
    
* `labels` → used for selection/grouping
    
* `annotations` → extra info, not used for selection
    

<mark>S = SPEC:</mark>

This is the most important part of your configuration manifest. It defines the desired state of your resources. Every resource type (Pod, Deployment, Service, etc.) has a different structure inside spec. This is where you define the real “meat” of your object: containers, replicas, ports, volumes, etc.

This is where you specify the kind of container image you want to run in your pod, the replicas of the pods you want, the configuration of the pods in terms of networking, volume binding, secrets and environmental variables connections, etc.

Save this file as: `pod-creation.yaml`

```bash
apiVersion: v1   
kind: Pod  
metadata: 
  name: my-nginx-pod   # Name of your pod
  namespace: default      # the namespace your pod should run. 
  labels:
    apptype: web       # add a key/pair label to distinguish pods
    tier: frontend
spec:
  containers:
    - name: nginx      # name of container inside the pod called my-nginx-pod 
      image: nginx:latest # the container image to use
      ports:             # the port for the nginx container in the pod
        - containerPort: 80
```

Now you can create your pod by using the command for Declarative Approach we discussed earlier.

```bash
# apply your configuration to create the pod
kubectl apply -f pod-creation.yaml
```

This will create your nginx pod successfully with all the information you specified.

```bash
# view your runnning pods
kubectl get pods
```

Congratulations! You’ve created your first Pod.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757801969123/fa5ef548-6f8d-4e1d-a3a5-a4b16b4382e2.png align="center")

## <s>Mic</s> Pods Checks!

Now that the pods has been created, you’ll need to check your pods status time after time. Whether to troubleshoot for issues causing your pods not to function well or you need information about your pods.

1. Getting Status of the Pod: The first and basic checks is to know if your pod is running and healthy. This is important and one check you will always carry out.
    

```bash
kubectl get pods
```

This command will list all your pods with their current status. You will be able to see the ones running, those that are crashing and those that are restarting. This command will also show how long the pod has been running and how many times the pods has restarted

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757801969123/fa5ef548-6f8d-4e1d-a3a5-a4b16b4382e2.png align="left")

### Checking Status of a Pod

It is important to know the status of your pod from time to time. This helps in troubleshooting your pods and figuring out some basic root-cause of your problem.

You can use `kubectl describe pod <pod-name>` and check the STATUS line of the long list. This is your POD STATE.

> This STATUS is the general pod status and it gives the overall state of the pod in question

The list of pod statuses are highlighted below:

* PENDING: This status happens either when your pod has not been assigned to a node by the Scheduler, when the image is still downloading or even when their is limited resources to even initiate the creation of the pod
    
* RUNNING: This is what you always want to see. Your pod has been assigned to a node, the pod has started and at least one container in the pod has started, starting or restarting.
    
* SUCCEEDED: This is always associated with short-lived pods where the is expected to be terminated after performing a task. A good example is CRONJOB. The status means that all the containers in the pod has been terminated successfully.
    
* FAILED: This is partially similar to SUCCEEDED because it means all the containers in the pod has been terminated. But the difference here is that at least one of the container got terminated because of failure without restarting. This could be due to limit (OOM - out of memory, restart policy set to never, application error when exceptions are not handled, etc). Since the criteria set for the container to run (In Spec) is not met and no restart is specified, kubernetes set the status FAILED to the pod.
    
* UNKNOWN: This status occurs when the API server is having issues with communicating with the pod to know it’s status - majorly the kubelet of the pod due to network issue, node crash or kubelet itself dying.
    

Another kind of status is the CONTAINER STATUS. This can be gotten from the containers line when you use `Kubectl describe` command. You will see the STATE and REASON.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758004010734/2581c269-012a-4da1-b1c1-9da2e1b259f0.png align="center")

> Here, the container in out pod is running smoothly as seen under the STATE. We can also see the last state = Terminated and the Reason for being Terminated to be UNKNOWN.

When you use `kubectl get po` to check your pod, the STATUS column gives the summary of both the STATE and REASON of the ContainerStatus.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757801969123/fa5ef548-6f8d-4e1d-a3a5-a4b16b4382e2.png align="left")

The statuses you can see at this point aside Running are as follows:

* CrashLoopBackOff: This happens when the container inside the pod keeps crashing and the kunernetes enters a backoff of exponential restarts just for the container to stabilizes. This is technically the WAITING state of the container
    
* ImagePullBackOff/ErrImagePull: This happens when there is an error in pulling the image from the registry either missing/incorrect image, secret or access to the registry, etc. This is also not a phase for pod but rather a REASON for Container STATE = Waiting.
    
* ContainerCreating: This is the state where your pods has been scheduled to a node and the container is currently being created in the pod. This is an intermediate phase before RUNNING
    
* Terminating: Kubernetes is shutting down especially if you use `kubectl delete pods <Pod-Name>`.
    

## Other Pod details to check

Checking the Details of our Pods: You’ll want to check some specific details of your Pod such as

* the Container info
    
* state of the pod
    
* Volumes attached
    
* namespace and all the metadata
    

These gives you the information about the events that happened inside the pod. This is a log of event that has happened on the pod

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758004581070/21a0c7d9-227c-4ced-b1a8-dd64e2dca723.png align="center")

### Working inside your Pod:

There will be time you will want to enter your pod and do some configuration (where possible). To go inside the pod, you will need to use the `kubectl exec - - -`

```bash
# Usage
kubectl exec -it my-nginx-pod -- sh
```

This is as if you are ssh-ing into your pod (figuratively). Now, you’ll be able to navigate around inside your pod.

### Deleting your Pod:

To delete your pod, you’ll run `kubectl delete pod - - -` command

```bash
kubectl delete pod <pod-name>
```

After running this command, you can quickly check the status of the pod using kubectl get pods, you will see the status being TERMINATING. This is to show you that the pod is in the deletion phase.
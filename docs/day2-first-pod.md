Kubernetes Pod Deployment using Nginx
Objective

The objective of this project is to learn the basic Kubernetes architecture by deploying an Nginx application inside a Pod using a YAML manifest. This project demonstrates how Kubernetes manages containerized applications and how to interact with Pods using kubectl.

1. What is a Pod?

A Pod is the smallest deployable unit in Kubernetes. It is the object that runs one or more containers that share the same network and storage.

In most real-world scenarios, a Pod contains one container, but it can also contain multiple tightly coupled containers that work together.

Example
Pod
│
└── Nginx Container
Key Points
Smallest Kubernetes object
Runs one or more containers
Shares IP address and storage among containers
Managed by Kubernetes
2. Why are Pods Used?

Kubernetes does not manage containers directly. Instead, it manages Pods because Pods provide additional capabilities such as:

Shared networking
Shared storage (Volumes)
Automatic container restart
Resource management
Easy scaling and scheduling
Benefits
Better application management
High availability
Easy scaling
Simplified networking
Self-healing capabilities
3. Kubernetes Architecture
                  Kubernetes Cluster
                         │
          ┌──────────────┴──────────────┐
          │                             │
     Control Plane                Worker Node
                                        │
                                   ┌────┴────┐
                                   │         │
                                 Pod       Pod
                                  │
                             Nginx Container
Components
Cluster

The complete Kubernetes environment containing all nodes and resources.

Node

A physical or virtual machine where Pods are executed.

Pod

The smallest deployable Kubernetes object that hosts containers.

Container

Runs the actual application (Nginx in this project).

4. YAML File Explanation
nginx-pod.yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
Explanation
Field	Purpose
apiVersion	Kubernetes API version used
kind	Type of Kubernetes resource (Pod)
metadata	Information about the resource, such as its name
spec	Desired state/configuration of the Pod
containers	List of containers inside the Pod
name	Name of the container
image	Docker image to run
ports	Container ports exposed
containerPort	Port on which the application listens
5. Commands Used
Start Minikube
minikube start
Check Minikube Status
minikube status
Check Cluster Information
kubectl cluster-info
List Nodes
kubectl get nodes
List Namespaces
kubectl get namespaces
Deploy the Pod
kubectl apply -f nginx-pod.yaml
Check Pods
kubectl get pods
View Pod Details
kubectl describe pod nginx-pod
Forward Local Port
kubectl port-forward nginx-pod 8080:80
Open in Browser
http://localhost:8080
Delete Pod
kubectl delete -f nginx-pod.yaml
6. Screenshots

Add the following screenshots to your GitHub repository.

1. Running Pod

Take a screenshot after running:

kubectl get pods

Example:

NAME          READY   STATUS    RESTARTS   AGE
nginx-pod     1/1     Running   0          30s
2. Pod Details

Take a screenshot after running:

kubectl describe pod nginx-pod

Include details such as:

Pod Name
Node
IP Address
Container Image
Events
Status
3. Nginx Welcome Page

After port forwarding:

kubectl port-forward nginx-pod 8080:80

Open:

http://localhost:8080

Take a screenshot of the Welcome to nginx! page.

Project Workflow
Write YAML
      │
      ▼
kubectl apply -f nginx-pod.yaml
      │
      ▼
API Server
      │
      ▼
Scheduler
      │
      ▼
Node
      │
      ▼
Pod
      │
      ▼
Nginx Container
      │
      ▼
kubectl port-forward
      │
      ▼
Browser (http://localhost:8080)
Learning Outcomes

After completing this project, I learned:

Kubernetes architecture
Cluster, Node, Pod, and Container concepts
Writing Kubernetes YAML manifests
Deploying Pods using kubectl apply
Inspecting Pods with kubectl get and kubectl describe
Accessing applications using kubectl port-forward
Basic Kubernetes troubleshooting
Interview Questions
What is a Pod?

A Pod is the smallest deployable unit in Kubernetes that runs one or more containers sharing networking and storage.

Why are Pods used?

Pods provide networking, storage sharing, lifecycle management, and allow Kubernetes to manage containers effectively.

Difference between Pod and Container?

A Container runs an application, while a Pod is a Kubernetes object that hosts one or more containers.

What is the purpose of kubectl apply?

It creates or updates Kubernetes resources based on the desired state defined in a YAML file.

What is the purpose of kubectl describe pod?

It displays detailed information about a Pod, including its status, events, node, IP, and container details.

What is kubectl port-forward used for?

It creates a temporary tunnel from your local machine to a Pod, allowing you to access the application without exposing it through a Kubernetes Service.

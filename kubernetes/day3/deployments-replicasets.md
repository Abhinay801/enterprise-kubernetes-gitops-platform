Kubernetes Deployments and ReplicaSets
Objective

The objective of this project is to understand how Kubernetes Deployments manage Pods using ReplicaSets. This project demonstrates automatic Pod management, scaling applications, and Kubernetes self-healing capabilities.

1. Difference Between Pods and Deployments
Pod	Deployment
Smallest deployable unit in Kubernetes	Manages one or more Pods
Runs one or more containers	Creates and manages ReplicaSets
Does not automatically recover if deleted	Automatically recreates deleted Pods
Manual scaling	Automatic scaling using replicas
Suitable for testing	Suitable for production environments
Pod

A Pod is the smallest Kubernetes object that runs one or more containers.

Pod
│
└── Nginx Container

Example:

apiVersion: v1
kind: Pod
Deployment

A Deployment manages Pods declaratively. It ensures the required number of Pods are always running.

Example:

apiVersion: apps/v1
kind: Deployment
2. What is a ReplicaSet?

A ReplicaSet ensures that the desired number of Pod replicas are always running.

Example:

spec:
  replicas: 3

means:

Always keep 3 Pods running.

Architecture:

Deployment
      │
      ▼
ReplicaSet
      │
 ┌────┼────┐
 │    │    │
Pod1 Pod2 Pod3

The Deployment creates the ReplicaSet, and the ReplicaSet creates and maintains the Pods.

3. Scaling

Scaling means increasing or decreasing the number of running Pods.

Scale to 5 Pods
kubectl scale deployment nginx-deployment --replicas=5

Check the result:

kubectl get deployments

Output:

NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   5/5     5            5

Check Pods:

kubectl get pods

You should see 5 Pods.

Scale Down
kubectl scale deployment nginx-deployment --replicas=2

Now Kubernetes removes the extra Pods and keeps only 2 running.

4. Self-Healing

One of Kubernetes' most important features is self-healing.

If a Pod managed by a Deployment is deleted manually:

kubectl delete pod <pod-name>

The ReplicaSet immediately creates a new Pod.

Example:

Before deletion:

Pod-A
Pod-B
Pod-C

Delete Pod-B:

kubectl delete pod Pod-B

Kubernetes automatically creates a replacement:

Pod-A
Pod-C
Pod-D

The total number of Pods remains unchanged because the ReplicaSet maintains the desired replica count.

5. Commands Used
Start Minikube
minikube start
Deploy the Deployment
kubectl apply -f nginx-deployment.yaml
View Deployments
kubectl get deployments
View ReplicaSets
kubectl get replicasets
View Pods
kubectl get pods
Watch Pods in Real Time
kubectl get pods -w
Scale Up
kubectl scale deployment nginx-deployment --replicas=5
Scale Down
kubectl scale deployment nginx-deployment --replicas=2
Delete a Pod
kubectl delete pod <pod-name>
Describe Deployment
kubectl describe deployment nginx-deployment
6. Screenshots

Include the following screenshots in your GitHub repository:

Deployment Status

Command:

kubectl get deployments

Capture the output showing the Deployment status.

ReplicaSet Status

Command:

kubectl get replicasets

Capture the ReplicaSet details.

Pod Status

Command:

kubectl get pods

Capture the list of running Pods.

Scaling Demonstration

Take screenshots of:

Scale up:

kubectl scale deployment nginx-deployment --replicas=5
kubectl get pods

Scale down:

kubectl scale deployment nginx-deployment --replicas=2
kubectl get pods

These screenshots demonstrate Kubernetes automatically creating and deleting Pods to match the requested replica count.

Project Workflow
Write Deployment YAML
          │
          ▼
kubectl apply -f nginx-deployment.yaml
          │
          ▼
Deployment
          │
          ▼
ReplicaSet
          │
          ▼
Pods
          │
          ▼
Containers (Nginx)
Learning Outcomes

After completing this project, I learned:

The difference between Pods and Deployments.
How Deployments manage Pods using ReplicaSets.
How ReplicaSets maintain the desired number of Pods.
How to scale applications up and down.
How Kubernetes automatically replaces deleted Pods (self-healing).
How to inspect Deployments, ReplicaSets, and Pods using kubectl.
Interview Questions
1. What is the difference between a Pod and a Deployment?

A Pod runs one or more containers. A Deployment manages Pods by ensuring the desired number of replicas are running and supports scaling, rolling updates, and self-healing.

2. What is a ReplicaSet?

A ReplicaSet ensures that the specified number of Pod replicas are always running in the cluster.

3. Does a Deployment create Pods directly?

No. The workflow is:

Deployment
     ↓
ReplicaSet
     ↓
Pods
4. What is scaling in Kubernetes?

Scaling is the process of increasing or decreasing the number of Pod replicas to match application demand.

5. What is self-healing?

Self-healing is Kubernetes' ability to automatically replace failed or deleted Pods to maintain the desired state defined by the Deployment.

6. Which command is used to scale a Deployment?
kubectl scale deployment nginx-deployment --replicas=<number>

For example:

kubectl scale deployment nginx-deployment --replicas=5

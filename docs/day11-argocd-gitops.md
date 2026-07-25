Day 11 – GitOps with Argo CD
Overview

Today, I learned how to implement GitOps using Argo CD in Kubernetes. Instead of manually applying Kubernetes manifests, Argo CD continuously monitors a Git repository and synchronizes the Kubernetes cluster to match the desired state defined in Git.

This approach makes deployments automated, repeatable, and easier to manage in production environments.

What is GitOps?

GitOps is a deployment methodology where Git is the single source of truth for infrastructure and application configurations.

Instead of manually deploying applications using commands like:

kubectl apply -f deployment.yaml

all changes are made by updating files in a Git repository.

Whenever changes are pushed to Git, a GitOps tool (such as Argo CD) detects those changes and automatically updates the Kubernetes cluster.

GitOps Workflow
Developer
    │
    ▼
Edit Kubernetes Manifests
    │
    ▼
Git Commit
    │
    ▼
Git Push
    │
    ▼
Git Repository
    │
    ▼
Argo CD
    │
    ▼
Kubernetes Cluster
Advantages
Automated deployments
Version-controlled infrastructure
Easy rollback
Better collaboration
Reduced manual errors
Self-healing infrastructure
Full audit history
What is Argo CD?

Argo CD is an open-source GitOps Continuous Delivery (CD) tool designed specifically for Kubernetes.

It continuously monitors both:

Git Repository
Kubernetes Cluster

If there is any difference between Git and the cluster, Argo CD synchronizes the cluster automatically.

Responsibilities of Argo CD
Deploy applications from Git
Monitor application status
Detect configuration drift
Synchronize cluster state
Roll back deployments
Self-heal applications
Source of Truth

The Source of Truth is the location that contains the desired configuration of the system.

In GitOps:

Git Repository = Source of Truth

This means:

Deployments
Services
ConfigMaps
Secrets
Ingress
Helm Charts

are all stored inside Git.

No manual changes should be made directly inside the Kubernetes cluster.

Why?

Because Git always contains the latest approved configuration.

If someone manually changes a deployment:

Cluster
↓

Deployment changed manually

Argo CD compares it with Git.

If they are different:

Git
↓

Desired State

↓

Argo CD

↓

Cluster Restored
Sync

Synchronization (Sync) means applying the configuration stored in Git to the Kubernetes cluster.

Example:

Developer changes:

replicaCount: 3

Commit:

git commit

Push:

git push

Argo CD detects the change and updates Kubernetes automatically.

Manual Sync

If Auto Sync is disabled, click:

SYNC

inside the Argo CD dashboard.

Auto Sync

Auto Sync allows Argo CD to automatically synchronize the cluster whenever changes are pushed to Git.

Without Auto Sync:

Git Push

↓

Argo CD detects changes

↓

Waiting for Manual Sync

With Auto Sync:

Git Push

↓

Argo CD detects changes

↓

Cluster Updated Automatically
Benefits
No manual deployments
Faster releases
Consistent environments
Reduced operational effort
Self Heal

Self Heal automatically restores the cluster when someone changes resources manually.

Example:

Someone deletes a Deployment:

kubectl delete deployment nginx

Argo CD compares the cluster with Git.

Since Git still contains the Deployment:

Git

↓

Deployment Exists

↓

Argo CD

↓

Deployment Recreated
Benefits
Protects against accidental changes
Automatically fixes configuration drift
Maintains desired state
Prune

Prune removes Kubernetes resources that no longer exist in Git.

Example:

Git before:

deployment.yaml
service.yaml
configmap.yaml

Later:

deployment.yaml
service.yaml

ConfigMap is deleted from Git.

Argo CD automatically removes the ConfigMap from Kubernetes.

Benefits
Removes unused resources
Prevents stale objects
Keeps the cluster clean
Ensures Git and cluster remain identical
Commands Used
Create Argo CD Namespace
kubectl create namespace argocd

Creates a dedicated namespace for all Argo CD components.

Install Argo CD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Installs all Argo CD components into the argocd namespace.

Check Argo CD Pods
kubectl get pods -n argocd

Displays all Argo CD pods and their status.

Expected components include:

argocd-server
argocd-repo-server
argocd-application-controller
argocd-dex-server
argocd-redis
Check Services
kubectl get svc -n argocd

Lists services running inside the Argo CD namespace.

Useful for finding the argocd-server service and its access method.

Get Minikube IP
minikube ip

Displays the IP address of the Minikube cluster, which is used to access Argo CD when exposed via NodePort.

Learning Summary

Today I learned:

The concept of GitOps.
Why Git is the Source of Truth.
How Argo CD automates Kubernetes deployments.
The difference between manual deployment and GitOps.
What Sync, Auto Sync, Self Heal, and Prune do.
How to install and verify Argo CD.
How Argo CD continuously keeps the Kubernetes cluster synchronized with Git.
Key Takeaways
GitOps = Git-driven deployment model
Argo CD = GitOps Continuous Delivery tool
Git Repository = Source of Truth
Sync = Apply Git changes to Kubernetes
Auto Sync = Automatic deployment
Self Heal = Restore resources if manually modified
Prune = Remove resources deleted from Git
Interview Questions
1. What is GitOps?

Answer:
GitOps is a deployment methodology where Git acts as the single source of truth. Any changes committed to Git are automatically synchronized with the Kubernetes cluster using a GitOps tool such as Argo CD.

2. What is Argo CD?

Answer:
Argo CD is an open-source GitOps Continuous Delivery tool that monitors Git repositories and automatically deploys or synchronizes Kubernetes applications based on the desired state stored in Git.

3. What is the Source of Truth in GitOps?

Answer:
The Git repository is the Source of Truth because it stores the desired state of the Kubernetes infrastructure and applications.

4. What is Self Heal in Argo CD?

Answer:
Self Heal automatically restores Kubernetes resources if someone manually changes or deletes them, ensuring the cluster always matches the desired state stored in Git.

5. What is Prune?

Answer:
Prune automatically deletes Kubernetes resources that have been removed from the Git repository, keeping the cluster synchronized with Git

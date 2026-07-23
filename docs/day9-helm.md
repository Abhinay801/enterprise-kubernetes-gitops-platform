Day 13 – Helm Package Manager
Objective

The objective of this project is to learn Helm, the package manager for Kubernetes. Helm simplifies application deployment by packaging Kubernetes manifests into reusable Charts, allowing applications to be installed, upgraded, rolled back, and removed using simple commands.
What is Helm?

Helm is the package manager for Kubernetes.

Just like:

APT manages software packages on Ubuntu.
npm manages JavaScript packages.
pip manages Python packages.

Helm manages Kubernetes applications.

Instead of writing and applying many YAML files manually, Helm packages them into a single reusable unit called a Chart.

Without Helm

Imagine deploying an application with:

Deployment
Service
ConfigMap
Secret
PVC
Ingress

You would run:

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f pvc.yaml
kubectl apply -f ingress.yaml

Managing many files becomes difficult.

With Helm

You simply run:

helm install enterprise-app .

Helm installs all required Kubernetes resources in one step.

Why Helm?

Helm provides several advantages:

Reusable: Package applications into reusable Charts.
Configurable: Customize deployments using values.yaml.
Versioned: Track application versions and releases.
Upgradable: Update applications without redeploying everything.
Rollback: Restore a previous version if an upgrade fails.
Easy Deployment: Install complex applications with a single command.
What is a Chart?

A Chart is a package containing everything required to deploy an application.

It includes:

Kubernetes YAML templates
Default configuration (values.yaml)
Metadata (Chart.yaml)
Helper templates
Architecture
                 Helm Chart
                     │
      ┌──────────────┼──────────────┐
      ▼              ▼              ▼
 Chart.yaml      values.yaml     templates/
                                     │
                                     ▼
                          Deployment
                          Service
                          ConfigMap
                          Secret
                          PVC
                          Ingress
What is a Release?

A Release is a running instance of a Helm Chart in a Kubernetes cluster.

Example:

Chart:

enterprise-app

Install command:

helm install enterprise-app .

Creates a Release named:

enterprise-app

You can install the same Chart multiple times with different release names:

helm install dev .
helm install test .
helm install prod .

All use the same Chart but create separate deployments.

What is values.yaml?

values.yaml stores configurable values used by the Helm templates.

Example:

replicaCount: 3

image:
  repository: nginx
  tag: latest

The Deployment template uses these values:

replicas: {{ .Values.replicaCount }}

Helm replaces the placeholders with the actual values during installation.

What are Templates?

Templates are Kubernetes YAML files containing placeholders.

Example:

replicas: {{ .Values.replicaCount }}

image: {{ .Values.image.repository }}

When Helm installs the chart, it renders the templates into normal Kubernetes manifests.

Example output:

replicas: 3

image: nginx
Helm Architecture
                  values.yaml
                       │
                       ▼
                Helm Templates
                       │
               Replace Variables
                       │
                       ▼
          Kubernetes YAML Manifests
                       │
                       ▼
              Kubernetes Cluster
Helm Lifecycle
1. Create a Chart
helm create enterprise-app

Creates the default Helm chart structure.

2. Lint the Chart
helm lint .

Checks the chart for syntax, template, and structure errors.

Expected:

1 chart(s) linted, 0 chart(s) failed
3. Install the Chart
helm install enterprise-app .

Creates a Release and deploys all Kubernetes resources.

4. View Installed Releases
helm list

Displays all installed Helm releases.

Example:

NAME             STATUS      REVISION
enterprise-app   deployed    1
5. Upgrade the Release

Modify values.yaml, for example:

replicaCount: 3

Then run:

helm upgrade enterprise-app .

Helm updates only the changed resources.

6. View Release History
helm history enterprise-app

Example:

REVISION    STATUS
1           deployed
2           deployed

Each upgrade creates a new revision.

7. Roll Back

Restore an earlier version:

helm rollback enterprise-app 1

Helm reverts the release to revision 1.

8. Uninstall
helm uninstall enterprise-app

Deletes all Kubernetes resources created by the release.

9. Package the Chart
helm package .

Creates a distributable package:

enterprise-app-0.1.0.tgz

This .tgz file can be shared or uploaded to a Helm repository.

Helm Lifecycle Diagram
            helm create
                 │
                 ▼
           Create Chart
                 │
                 ▼
            helm lint
                 │
                 ▼
          Validate Chart
                 │
                 ▼
          helm install
                 │
                 ▼
          Create Release
                 │
                 ▼
         helm upgrade
                 │
                 ▼
         New Revision
                 │
                 ▼
         helm history
                 │
                 ▼
      View All Revisions
                 │
        ┌────────┴────────┐
        ▼                 ▼
helm rollback      helm uninstall
        │                 │
        ▼                 ▼
 Previous Version     Delete Release
Commands Used
Check Helm Version
helm version
Create a New Chart
helm create enterprise-app
Validate the Chart
helm lint .
Install the Chart
helm install enterprise-app .
View Installed Releases
helm list
Upgrade the Release
helm upgrade enterprise-app .
View Release History
helm history enterprise-app
Roll Back to Revision 1
helm rollback enterprise-app 1
Uninstall the Release
helm uninstall enterprise-app
Package the Chart
helm package .
Screenshots to Include
helm version
helm create enterprise-app
helm lint .
helm install enterprise-app .
helm list
helm history enterprise-app
helm rollback enterprise-app 1
helm uninstall enterprise-app
helm package .
Interview Questions
1. What is Helm?

Helm is the package manager for Kubernetes. It packages Kubernetes manifests into reusable Charts and simplifies application deployment, upgrades, rollbacks, and management.

2. Why do we use Helm?

Helm reduces the complexity of managing multiple Kubernetes YAML files by packaging them into reusable Charts. It supports versioning, configuration through values.yaml, upgrades, rollbacks, and easy sharing of applications.

3. What is a Chart?

A Chart is a package containing Kubernetes templates, default configuration, and metadata required to deploy an application.

4. What is a Release?

A Release is a deployed instance of a Helm Chart in a Kubernetes cluster. Multiple releases can be created from the same Chart using different release names.

5. What is values.yaml?

values.yaml is the configuration file that stores customizable values used by Helm templates. It allows the same Chart to be deployed with different settings without modifying the template files.

6. What are Helm Templates?

Templates are Kubernetes manifest files that contain placeholders such as {{ .Values.replicaCount }}. During installation, Helm replaces these placeholders with values from values.yaml to generate the final Kubernetes YAML.

7. Explain the Helm lifecycle.

The Helm lifecycle typically consists of:

Create a chart (helm create)
Lint the chart (helm lint)
Install the chart (helm install)
Upgrade the release (helm upgrade)
View history (helm history)
Rollback if needed (helm rollback)
Uninstall the release (helm uninstall)
Package the chart for distribution (helm package)

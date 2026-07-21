What is a Multi-Tier Application?

A multi-tier application is an application divided into multiple layers (tiers), where each tier has a specific responsibility.

For your project:

Tier 1 → Frontend
        PHPMyAdmin

Tier 2 → Database
        MySQL

Instead of putting everything in one container, each component runs separately.

Architecture
                   User
                     │
                     │
             Browser Request
                     │
                     ▼
          NodePort Service (PHPMyAdmin)
                     │
                     ▼
        PHPMyAdmin Deployment/Pod
                     │
     Reads DB credentials from
      ConfigMap + Secret
                     │
                     ▼
         ClusterIP Service (MySQL)
                     │
                     ▼
          MySQL Deployment/Pod
                     │
                     ▼
          PersistentVolumeClaim
                     │
                     ▼
          PersistentVolume (1Gi)
                     │
                     ▼
        /data/pv-demo (Disk)
Components Created
1. MySQL Deployment

Created using:

kind: Deployment

Runs the MySQL container.

Example:

MySQL Pod
     │
     ▼
Database Engine
2. MySQL Service

Created using:

kind: Service

Type:

type: ClusterIP

Purpose:

Allows other Pods to reach MySQL.

Why ClusterIP for MySQL?

Because MySQL should not be accessible from outside the cluster.

Only applications inside Kubernetes should connect.

Browser
   │
   │ ❌ Cannot access
   ▼

Cluster

PHPMyAdmin
     │
     ▼
ClusterIP
     │
     ▼
MySQL

This improves security.

3. PHPMyAdmin Deployment

Runs the PHPMyAdmin container.

It provides the web interface.

Browser

↓

PHPMyAdmin

↓

MySQL
4. PHPMyAdmin Service

Type:

NodePort
Why NodePort?

Because you want to open it in your browser.

NodePort exposes the application outside the cluster.

Browser

↓

NodePort

↓

PHPMyAdmin Pod

Without NodePort, you couldn't access PHPMyAdmin from your laptop.

Difference
ClusterIP

Internal Only

Pod → Service → Pod
NodePort

External Access

Browser → NodePort → Pod
ConfigMap

You created something like:

data:
  MYSQL_DATABASE: enterprise_db

This stores non-sensitive configuration.

Examples:

Database name
Environment
Port
Hostname

Flow:

ConfigMap

↓

MYSQL_DATABASE

↓

MySQL Container
Secret

Example:

stringData:
  MYSQL_ROOT_PASSWORD: root123

Stores sensitive values:

Password
Tokens
API Keys

Flow:

Secret

↓

MYSQL_ROOT_PASSWORD

↓

MySQL Container
Why separate ConfigMap and Secret?

Suppose tomorrow the password changes.

Instead of rebuilding the container:

Old Password

↓

Update Secret

↓

Restart Pod

↓

Done

No code changes.

PersistentVolume

Created first.

PV

↓

1 GB Disk
PersistentVolumeClaim

Requests storage.

PVC

↓

1 GB Requested
How PVC Keeps MySQL Data Persistent

Without PVC

MySQL Pod

↓

Stores Database

↓

Delete Pod

↓

Database Lost ❌

With PVC

MySQL Pod

↓

PVC

↓

PV

↓

Disk

↓

Delete Pod

↓

Database Still Exists ✅

That's why databases always use Persistent Volumes.

Complete Architecture
                    USER
                      │
                      ▼
              Browser (Chrome)
                      │
                      ▼
            NodePort Service
          (PHPMyAdmin Service)
                      │
                      ▼
           PHPMyAdmin Deployment
                      │
      Reads ConfigMap & Secret
                      │
                      ▼
          ClusterIP Service
             (MySQL Service)
                      │
                      ▼
            MySQL Deployment
                      │
                      ▼
                  PVC
                      │
                      ▼
                  PV
                      │
                      ▼
             Local Storage
Commands Used

Deploy all YAML files:

kubectl apply -f .

View all Kubernetes resources:

kubectl get all

View Persistent Volumes:

kubectl get pv

View Persistent Volume Claims:

kubectl get pvc

View ConfigMaps:

kubectl get configmap

View Secrets:

kubectl get secrets

View Services:

kubectl get svc

View Pods:

kubectl get pods

Describe a Pod:

kubectl describe pod <pod-name>

View logs:

kubectl logs <pod-name>
Screenshots to Include

Take screenshots of:

All resources
kubectl get all
Persistent Volume Claim
kubectl get pvc
PHPMyAdmin Login Page

Open in browser and capture the login screen.

PHPMyAdmin Dashboard

After logging in successfully, capture the dashboard showing the database.

Interview Questions
1. What is a multi-tier application?

A multi-tier application separates an application into different layers, such as a frontend, backend, and database. Each tier performs a specific role and communicates with the others through services.

2. Why use ClusterIP for MySQL?

ClusterIP makes the MySQL service accessible only within the Kubernetes cluster, preventing direct external access and improving security.

3. Why use NodePort for PHPMyAdmin?

NodePort exposes PHPMyAdmin outside the cluster so users can access it from a web browser for database administration.

4. How were ConfigMaps and Secrets used?
ConfigMap stored non-sensitive configuration such as the database name.
Secret stored sensitive information such as the MySQL root password.

Both were injected into the containers as environment variables.

5. How does a PVC keep MySQL data persistent?

The MySQL Pod writes data to a PersistentVolumeClaim, which is bound to a PersistentVolume. Because the storage exists independently of the Pod, the database files remain even if the MySQL Pod is deleted and recreated. This prevents data loss and is essential for stateful applications like databases.

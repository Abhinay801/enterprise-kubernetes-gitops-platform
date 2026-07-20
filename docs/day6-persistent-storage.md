Day 6 – Kubernetes Persistent Volumes (PV) & Persistent Volume Claims (PVC)
Objective

The objective of this project is to understand how Kubernetes provides persistent storage to applications. By using Persistent Volumes (PV) and Persistent Volume Claims (PVC), applications can retain data even if Pods are restarted, deleted, or recreated.

1. What is a Persistent Volume (PV)?

A Persistent Volume (PV) is a storage resource in Kubernetes that provides permanent storage for applications.

It is created by a cluster administrator (or dynamically by a StorageClass) and represents actual storage such as:

Local disk (hostPath)
NFS
AWS EBS
Azure Disk
Google Persistent Disk
Ceph
Other storage systems

A PV exists independently of Pods, so deleting a Pod does not delete the stored data.

Example
PersistentVolume (PV)
        │
        ▼
1 GiB Storage
2. What is a Persistent Volume Claim (PVC)?

A Persistent Volume Claim (PVC) is a request for storage made by an application.

Instead of directly using a Persistent Volume, a Pod requests storage through a PVC.

The PVC specifies requirements such as:

Storage size
Access mode

Kubernetes automatically matches the PVC with a suitable PV.

Example
Pod
 │
 ▼
PersistentVolumeClaim (PVC)
 │
 ▼
PersistentVolume (PV)
 │
 ▼
Actual Storage
3. Difference Between Ephemeral and Persistent Storage
Ephemeral Storage	Persistent Storage
Temporary	Permanent
Data is lost when the Pod is deleted	Data remains after Pod deletion
Stored inside the container filesystem	Stored in a Persistent Volume
Suitable for temporary files	Suitable for databases, uploads, logs, and user data
Ephemeral Storage
Pod
 │
 ▼
Container Filesystem

Delete Pod
     │
     ▼
Data Lost ❌
Persistent Storage
Pod
 │
 ▼
PVC
 │
 ▼
PV
 │
 ▼
Disk

Delete Pod
     │
     ▼
Data Still Exists ✅
4. Storage Architecture
                   Kubernetes Cluster

                  +------------------+
                  |      Pod         |
                  |------------------|
                  | Nginx Container  |
                  +------------------+
                           │
                           ▼
                    volumeMount
                           │
                           ▼
                 PersistentVolumeClaim
                      (pvc-demo)
                           │
                           ▼
                  PersistentVolume
                     (pv-demo)
                           │
                           ▼
                  /data/pv-demo
                  (Actual Storage)
Data Flow
The Pod needs persistent storage.
It mounts a PersistentVolumeClaim (PVC).
The PVC is bound to a PersistentVolume (PV).
The PV points to the actual storage location.
Data written by the Pod is stored on the Persistent Volume and survives Pod restarts.
5. Commands Used
Create the Persistent Volume
kubectl apply -f persistent-volume.yaml
Verify the Persistent Volume
kubectl get pv
Create the Persistent Volume Claim
kubectl apply -f persistent-volume-claim.yaml
Verify the Persistent Volume Claim
kubectl get pvc
Deploy the Pod
kubectl apply -f nginx-storage.yaml
Verify the Pod
kubectl get pods
Enter the Pod
kubectl exec -it nginx-storage -- /bin/sh
Read the HTML file
cat /usr/share/nginx/html/index.html
Delete the Pod
kubectl delete pod nginx-storage
Recreate the Pod
kubectl apply -f nginx-storage.yaml

The data remains available because it is stored on the Persistent Volume.

6. Screenshots

Include the following screenshots in your GitHub repository:

Persistent Volumes

Command:

kubectl get pv

Capture the PV showing a Bound status.

Persistent Volume Claims

Command:

kubectl get pvc

Capture the PVC showing a Bound status.

Pods

Command:

kubectl get pods

Capture the running Pod (nginx-storage).

Reading Data from the Mounted Volume

Commands:

kubectl exec -it nginx-storage -- /bin/sh
cat /usr/share/nginx/html/index.html

Capture the terminal showing the contents of the file.

Learning Outcomes

After completing this project, I learned:

What a Persistent Volume (PV) is.
What a Persistent Volume Claim (PVC) is.
The difference between ephemeral and persistent storage.
How Pods use PVCs instead of directly accessing storage.
How Kubernetes binds PVCs to matching PVs.
Why persistent storage is essential for stateful applications such as databases and file storage.
Interview Questions
1. What is a Persistent Volume (PV)?

A Persistent Volume is a Kubernetes storage resource that represents storage available to the cluster. It exists independently of Pods and can be backed by local disks, network storage, or cloud storage.

2. What is a Persistent Volume Claim (PVC)?

A Persistent Volume Claim is a request for storage. A Pod uses a PVC to access storage without needing to know the details of the underlying storage system.

3. Why doesn't a Pod use a Persistent Volume directly?

A Pod uses a PVC to decouple the application from the storage implementation. This allows administrators to manage storage while developers simply request the storage they need.

4. What is the difference between ephemeral and persistent storage?

Ephemeral storage is temporary and is deleted when the Pod is deleted. Persistent storage is retained independently of the Pod and remains available after Pod restarts or recreation.

5. What does a Bound status mean?

A Bound status means Kubernetes has successfully matched a PersistentVolumeClaim with a suitable PersistentVolume. The requested storage is now reserved for that claim.

6. What types of applications need Persistent Volumes?

Applications that need to preserve data, such as:

MySQL and PostgreSQL databases
MongoDB
Elasticsearch
Jenkins
WordPress
File upload services
Logging and monitoring systems

These applications require storage that survives Pod restarts and rescheduling.

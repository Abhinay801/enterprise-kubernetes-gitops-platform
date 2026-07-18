Kubernetes Services
Objective

The objective of this project is to understand Kubernetes Services and how they provide stable network access to Pods. This project demonstrates exposing an Nginx Deployment using different Service types and understanding how Services communicate with Pods.

1. What is a Service?

A Service is a Kubernetes resource that provides a stable network endpoint to access one or more Pods.

Pods are temporary. If a Pod is deleted or recreated, its IP address changes. A Service solves this problem by providing a permanent IP address and DNS name that forwards traffic to the correct Pods.

Architecture
                Client
                   │
                   ▼
             Kubernetes Service
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      Pod 1      Pod 2      Pod 3
      (Nginx)    (Nginx)    (Nginx)
2. Why are Services Needed?

Without a Service:

Client
   │
   ▼
Pod IP (10.244.0.5)

If the Pod is deleted:

Pod deleted ❌
New Pod created
New IP = 10.244.0.8

The old IP no longer works.

With a Service:

Client
   │
   ▼
Service (Stable IP)
   │
   ▼
Current Running Pod(s)

The Service always forwards traffic to healthy Pods, even if Pods are recreated.

Benefits
Stable IP address
Built-in load balancing
Service discovery using DNS
Decouples clients from Pod IP changes
Works with Deployments and ReplicaSets
3. Types of Kubernetes Services
Service Type	Description	Use Case
ClusterIP	Accessible only inside the cluster	Internal communication between applications
NodePort	Exposes the Service on a port of every node	Development and testing
LoadBalancer	Creates an external load balancer (supported cloud environments)	Production applications
ClusterIP

Default Service type.

Internet
    ❌
     │
Cluster
     │
Service
     │
Pods

Accessible only from within the Kubernetes cluster.

Example:

spec:
  type: ClusterIP
NodePort

Exposes the Service on every node.

Browser
     │
http://NodeIP:30080
     │
     ▼
NodePort Service
     │
     ▼
Pods

Example:

spec:
  type: NodePort

In Minikube, you can access it using:

minikube service nginx-service
LoadBalancer

Used mainly in cloud providers such as AWS, Azure, or GCP.

Internet
      │
External Load Balancer
      │
      ▼
Kubernetes Service
      │
      ▼
Pods

Example:

spec:
  type: LoadBalancer

In Minikube, a real cloud load balancer is not created.

4. Service YAML Explanation

Example:

apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

  type: ClusterIP
Explanation
Field	Purpose
apiVersion	Kubernetes API version
kind	Resource type (Service)
metadata	Service name and metadata
selector	Selects Pods using labels
ports	Defines Service ports
port	Port exposed by the Service
targetPort	Port inside the Pod
type	Service type (ClusterIP, NodePort, or LoadBalancer)
How a Service Finds Pods

Deployment labels:

labels:
  app: nginx

Service selector:

selector:
  app: nginx

The labels must match.

Service
Looking for:
app=nginx
        │
        ▼
Pod
Label:
app=nginx
        ✔
5. Commands Used
Deploy the Deployment
kubectl apply -f nginx-deployment.yaml
Create the Service
kubectl apply -f nginx-service.yaml
View Services
kubectl get services
View Endpoints
kubectl get endpoints

Endpoints show the IP addresses of the Pods behind the Service.

Describe the Service
kubectl describe service nginx-service
Access the Service (Minikube)
minikube service nginx-service

This opens the Nginx page in your browser.

Delete the Service
kubectl delete service nginx-service
6. Screenshots

Include these screenshots in your GitHub repository:

Services

Command:

kubectl get services

Capture the list of Services.

Endpoints

Command:

kubectl get endpoints

Capture the Pod IPs associated with the Service.

Browser

Open the application using:

minikube service nginx-service

Take a screenshot of the Welcome to nginx! page.

Project Workflow
Browser
    │
    ▼
Kubernetes Service
    │
    ▼
Endpoints
    │
    ▼
Pods
    │
    ▼
Nginx Container
Learning Outcomes

After completing this project, I learned:

What a Kubernetes Service is and why it is required.
How Services provide a stable network endpoint for Pods.
The differences between ClusterIP, NodePort, and LoadBalancer.
How Services use label selectors to route traffic.
How to inspect Services and Endpoints using kubectl.
Interview Questions
1. What is a Kubernetes Service?

A Service is a Kubernetes resource that provides a stable network endpoint to access one or more Pods.

2. Why do we need a Service?

Pods are temporary and their IP addresses change when they are recreated. A Service provides a stable IP and DNS name so clients can reliably reach the application.

3. What is the default Service type?

ClusterIP is the default Service type.

4. What is the difference between port and targetPort?
port is the port exposed by the Service.
targetPort is the port on the Pod where the application is listening.

Example:

Browser
   │
   ▼
Service Port (80)
   │
   ▼
Pod Target Port (80)
5. How does a Service know which Pods to send traffic to?

It uses the selector field to match the labels on Pods. The selector and Pod labels must match exactly.

6. What is an Endpoint?

An Endpoint is the list of Pod IP addresses associated with a Service. Kubernetes automatically updates the Endpoint list as Pods are created or removed.

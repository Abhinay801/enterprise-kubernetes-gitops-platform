Day 8 – Kubernetes Ingress
Goal of Today

The goal was to expose the PHPMyAdmin application using Kubernetes Ingress instead of accessing it through a NodePort.

Previously, we accessed PHPMyAdmin using:

http://127.0.0.1:35849

or

http://127.0.0.1:33321

These ports are temporary and not ideal for production.

Today, we wanted to access it using a clean URL like:

http://192.168.49.2/phpmyadmin

This is where Ingress comes in.

What is Ingress?

Ingress is a Kubernetes object that controls how external users access applications inside the Kubernetes cluster.

Think of it as a traffic manager.

Instead of exposing every application separately, Ingress receives all incoming requests and decides where to send them.

For example:

Incoming Request

                http://company.com/
                           |
                    Ingress Controller
                           |
        ---------------------------------
        |               |              |
        |               |              |
      Web App      PHPMyAdmin      Grafana

Ingress itself doesn't route traffic. It only stores the rules.

The Ingress Controller reads those rules and forwards the traffic.

Why not use NodePort?

Suppose you have four applications.

Without Ingress:

Web App

http://192.168.49.2:30001

PHPMyAdmin

http://192.168.49.2:31395

Grafana

http://192.168.49.2:32000

Prometheus

http://192.168.49.2:32001

Problems:

Every application needs a different port.
Difficult to remember.
Looks unprofessional.
Hard to manage.

With Ingress:

http://company.com/

Web Application

http://company.com/phpmyadmin

PHPMyAdmin

http://company.com/grafana

Grafana

http://company.com/prometheus

Prometheus

One IP.

One domain.

Clean URLs.

What is an Ingress Controller?

Ingress is only a configuration file.

It does nothing by itself.

Someone must read that configuration and route the traffic.

That "someone" is called the Ingress Controller.

Today we installed:

NGINX Ingress Controller

using

minikube addons enable ingress

After enabling it, Kubernetes automatically created:

ingress-nginx namespace

Ingress Controller Pod

Controller Service

Admission Pods

We verified it by running

kubectl get pods -n ingress-nginx

The controller pod was running successfully.

Creating the Ingress

We created this YAML:

apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: enterprise-ingress

spec:
  ingressClassName: nginx

  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: phpmyadmin-service
            port:
              number: 80

This tells Kubernetes:

Whenever someone visits /, send the request to phpmyadmin-service on port 80.

How Request Flow Works

Suppose you open:

http://company.com/

The request travels like this:

Browser
      |
      |
      v
Ingress Controller
      |
      |
Reads Ingress Rules
      |
      |
      v
phpmyadmin-service
      |
      |
      v
PHPMyAdmin Pod
      |
      |
      v
MySQL Service
      |
      |
      v
MySQL Pod

Notice something important.

The browser never communicates directly with the Pod.

Everything goes through Services.

What We Verified

We checked:

kubectl get ingress

Output:

ADDRESS

192.168.49.2

This means Kubernetes assigned the Minikube IP to the Ingress.

Then we checked

kubectl describe ingress enterprise-ingress

Output showed

Backend

phpmyadmin-service:80

Everything looked correct.

Then we checked

kubectl get svc

Output

phpmyadmin-service

NodePort

80:31395

Service was working.

Then

kubectl get endpoints phpmyadmin-service

Output

10.244.0.44:80

This was very important.

Endpoints tell us which Pod the Service is forwarding traffic to.

If Endpoints had shown

<none>

the Service would not have known where to send traffic.

Since it showed

10.244.0.44:80

the Service was correctly connected to the PHPMyAdmin Pod.

The Problem

We tried opening

http://192.168.49.2/

Browser returned

ERR_CONNECTION_TIMED_OUT

Initially it looked like an Ingress problem.

But after checking everything, we realized Kubernetes was working correctly.

The Real Reason

Your setup is:

Windows
      |
      |
Google Chrome
      |
      |
WSL Ubuntu
      |
      |
Docker Desktop
      |
      |
Minikube

Minikube is running inside Docker.

Docker created its own private network.

192.168.49.2

belongs to that private network.

Windows cannot directly access that Docker network.

So the browser times out before it even reaches Kubernetes.

This is not an Ingress problem.

It is a networking limitation of Minikube with the Docker driver on WSL.

How We Confirmed It

We executed

minikube service phpmyadmin-service --url

Output:

http://127.0.0.1:33321

Opening this URL successfully displayed PHPMyAdmin.

This proved:

Deployment was correct.
Service was correct.
Pod networking was correct.
Kubernetes networking was correct.

The only issue was reaching the Minikube IP from Windows.

Overall Architecture
                    Windows Browser
                           |
                           |
             http://localhost:33321
                           |
          (Temporary tunnel created by Minikube)
                           |
                           |
                   Docker Desktop
                           |
                           |
                     WSL Ubuntu
                           |
                           |
                    Minikube Cluster
                           |
         -----------------------------------
         |                                 |
         |                          Kubernetes API
         |
NGINX Ingress Controller
         |
         |
Enterprise Ingress
         |
         |
phpmyadmin-service
         |
         |
PHPMyAdmin Pod
         |
         |
mysql-service
         |
         |
MySQL Pod
         |
         |
Persistent Volume
What I Learned Today

Today I learned:

What an Ingress is.
Why Ingress is preferred over NodePort.
What an Ingress Controller does.
How path-based routing works.
How Services connect to Pods using Endpoints.
How to troubleshoot Ingress by checking Pods, Services, Endpoints, and Ingress resources.
That ERR_CONNECTION_TIMED_OUT was caused by Docker Desktop + WSL networking, not by an incorrect Kubernetes configuration.
How minikube service creates a temporary tunnel so Windows can access applications running inside the Minikube cluster.

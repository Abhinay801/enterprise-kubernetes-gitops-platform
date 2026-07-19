Kubernetes ConfigMaps and Secrets
Objective

The objective of this project is to understand how Kubernetes separates application configuration from application code using ConfigMaps and securely stores sensitive information using Secrets. This approach improves security, flexibility, and maintainability in production environments.

1. What is a ConfigMap?

A ConfigMap is a Kubernetes resource used to store non-sensitive configuration data as key-value pairs.

Instead of hardcoding configuration values inside the application, the application reads them from a ConfigMap.

Examples of data stored in a ConfigMap:

Application name
Environment (Development, Testing, Production)
Database URL
Port numbers
Log level
Feature flags

Example:

APP_NAME=Enterprise DevOps

ENV=Production

DATABASE_URL=mysql.company.com

LOG_LEVEL=INFO
2. What is a Secret?

A Secret is a Kubernetes resource used to store sensitive information securely.

Examples include:

Database passwords
API keys
AWS Secret Access Keys
GitHub Personal Access Tokens
JWT Tokens
SSH Keys
TLS Certificates

Example:

DB_USERNAME=admin

DB_PASSWORD=password123

AWS_SECRET_ACCESS_KEY=xxxxxxxx

Secrets help avoid exposing sensitive information in source code or Git repositories.

3. Why Externalize Configuration?

Instead of writing configuration directly inside application code:

Database URL = mysql.company.com

Environment = Production

Store it outside the application.

Without External Configuration
Application
      │
Database URL Hardcoded

If the database changes:

mysql.company.com

↓

mysql-prod.company.com

You must:

Modify the source code
Rebuild the application
Build a new Docker image
Deploy again
With External Configuration
Application
      │
      ▼
ConfigMap
      │
Database URL

If the database changes, update only the ConfigMap.

No code changes.

No Docker image rebuild.

No application modification.

4. Difference Between ConfigMaps and Secrets
ConfigMap	Secret
Stores non-sensitive configuration	Stores sensitive information
Plain text	Stored as Base64-encoded values in Kubernetes (not encryption)
Environment variables	Passwords
URLs	API Keys
Port numbers	Tokens
Log levels	Certificates
Production Architecture
                 Kubernetes Cluster

             +---------------------+
             |     ConfigMap       |
             |---------------------|
             | APP_NAME            |
             | DATABASE_URL        |
             | ENV                 |
             +---------------------+
                       ▲
                       │
                 Reads Configuration
                       │
                       ▼
                +--------------+
                |     Pod      |
                | Application  |
                +--------------+
                       ▲
                       │
                 Reads Secrets
                       │
                       ▼
             +---------------------+
             |       Secret        |
             |---------------------|
             | DB_PASSWORD         |
             | API_KEY             |
             | AWS_SECRET_KEY      |
             +---------------------+
Commands Used
Create ConfigMap
kubectl create configmap app-config \
  --from-literal=APP_NAME="Enterprise DevOps" \
  --from-literal=ENV=Production \
  --from-literal=LOG_LEVEL=INFO
View ConfigMaps
kubectl get configmap
Describe ConfigMap
kubectl describe configmap app-config
Create Secret
kubectl create secret generic app-secret \
  --from-literal=DB_USERNAME=admin \
  --from-literal=DB_PASSWORD=password123
View Secrets
kubectl get secrets
Describe Secret
kubectl describe secret app-secret
Display Environment Variables Inside the Pod

First find the Pod:

kubectl get pods

Enter the Pod:

kubectl exec -it <pod-name> -- /bin/sh

Display environment variables:

printenv

This verifies that ConfigMap and Secret values are available inside the container.

Screenshots

Include the following screenshots in your GitHub repository:

ConfigMaps

Command:

kubectl get configmap
ConfigMap Details

Command:

kubectl describe configmap app-config
Secrets

Command:

kubectl get secrets
Environment Variables

Commands:

kubectl exec -it <pod-name> -- /bin/sh
printenv

Capture the terminal showing the injected environment variables.

Learning Outcomes

After completing this project, I learned:

What a ConfigMap is and how it stores application configuration.
What a Secret is and why it should be used for sensitive information.
Why externalizing configuration improves flexibility and maintainability.
How to create, inspect, and use ConfigMaps and Secrets.
How applications read configuration and secrets through environment variables.
Interview Questions
1. What is a ConfigMap?

A ConfigMap stores non-sensitive configuration data such as URLs, environment names, log levels, and application settings.

2. What is a Secret?

A Secret stores sensitive information such as passwords, API keys, tokens, and certificates.

3. Why should configuration be externalized?

Externalizing configuration allows configuration changes without modifying application code or rebuilding container images. It makes deployments more flexible and easier to manage.

4. What is the difference between a ConfigMap and a Secret?

ConfigMaps store non-sensitive configuration, while Secrets store sensitive data. Secrets are stored as Base64-encoded values in Kubernetes and are intended for confidential information.

5. Can a Secret be viewed?

Yes. Users with appropriate Kubernetes permissions can view and decode Secret values. Base64 encoding is not encryption, so additional security measures such as RBAC and encryption at rest are important in production.

6. How do Pods consume ConfigMaps and Secrets?

Pods can consume ConfigMaps and Secrets as:

Environment variables
Mounted volumes (files)
Command-line arguments (indirectly through environment or files)

This is the standard way to provide configuration and credentials to applications running in Kubernetes.

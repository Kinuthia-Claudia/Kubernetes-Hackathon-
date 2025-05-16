# Kubernetes-Hackathon-

## OverView
Welcome to the **Widgetario Kubernetes Hackathon**! This project showcases a complete containerized application modeled, deployed, and productionized in Kubernetes — from simple deployments to configuration, storage, Ingress, observability, and monitoring.
This repository includes the development of the Widgetario application, a public web application for a gadget-selling company, deployed on Kubernetes as part of a Kubernetes Course Labs Hackathon.

##  Project Structure

The app consists of:

- `products-db`: PostgreSQL database
- `products-api`: Java Spring Boot API for products
- `stock-api`: Go API managing stock levels
- `web`: .NET Core frontend web app

Prerequisites
Kubernetes Cluster: A local cluster (e.g., Minikube, Kind) or cloud-based cluster with at least 4 nodes and sufficient CPU/memory (8GB RAM, 4 CPUs recommended).
kubectl: Kubernetes command-line tool installed and configured.
Helm: Version 3.x for packaging and deploying the application.
Docker: For inspecting container images and building CI/CD artifacts.
Docker Hub Account: For pushing custom images in Part 7.
Git: For cloning the repository and managing source code.
Jenkins, Gogs, BuildKit: For CI/CD pipeline setup (Part 7).
Dependencies:
Prometheus and Grafana for monitoring.
EFK stack (Elasticsearch, Fluent Bit, Kibana) for logging.
NGINX Ingress Controller for routing.

Installation Instructions
1. Clone the Repository:

## Part 1 – Basic Deployment

We started by deploying all components using basic `Deployment` specs:

'''bash 
kubectl apply -f hackathon/solution-part-1/products-db \
              -f hackathon/solution-part-1/products-api \
              -f hackathon/solution-part-1/stock-api \
              -f hackathon/solution-part-1/web

## Part 2 – Secure & Dynamic Configuration
Security and flexibility were added by replacing hardcoded configs with Secrets, ConfigMaps, and environment variables.

Chnges that were made
1. Created a Kubernetes Secret for the PostgreSQL password.
2. Used ConfigMap to hold non-sensitive values like feature flags and URLs.
3. The .NET web app was configured to read from /app/secrets/api.json.


This code was ran to ensure all the deployed files were configured with the new code
kubectl apply -f hackathon/solution-part-2/products-db \
              -f hackathon/solution-part-2/products-api \
              -f hackathon/solution-part-2/stock-api \
              -f hackathon/solution-part-2/web
The out come was that  Secure configuration was enabled and  dark mode was also enabled via Widgetario__Theme=dark.

 ## Part 3 – Storage and Replication

We replaced the single Postgres deployment with a StatefulSet to support replication.
Used image was the image in widgetario/products-db:postgres-replicated

Added persistent volume claims

Set environment variables were POSTGRES_PASSWORD and POSTGRES_REPLICATION_PASSWORD
For Stock API

Used an ephemeral volume (emptyDir) for caching at /cache.

Ran this code 
kubectl delete deploy products-db
kubectl delete svc products-db
kubectl delete pvc -l app=products-db

kubectl apply -f hackathon/solution-part-3/products-db \
              -f hackathon/solution-part-3/products-api \
              -f hackathon/solution-part-3/stock-api \
              -f hackathon/solution-part-3/web

kubectl rollout restart deploy/products-api deploy/stock-api
The  Outcome was a Stateful DB with persistent storage and resilient caching.

🌐 Part 4 – Ingress and DNS
We deployed an Ingress Controller and exposed the services via custom hostnames.

Custom Domains:
http://widgetario.local (web)

http://api.widgetario.local/products (API)

Host Mapping:
bash
Copy
Edit
# Windows
./scripts/add-to-hosts.ps1 widgetario.local 127.0.0.1
./scripts/add-to-hosts.ps1 api.widgetario.local 127.0.0.1

# Linux/macOS
./scripts/add-to-hosts.sh widgetario.local 127.0.0.1
./scripts/add-to-hosts.sh api.widgetario.local 127.0.0.1
bash
Copy
Edit
kubectl apply -f hackathon/solution-part-4/ingress-controller \
              -f hackathon/solution-part-4/products-db \
              -f hackathon/solution-part-4/products-api \
              -f hackathon/solution-part-4/stock-api \
              -f hackathon/solution-part-4/web
✅ Outcome: User-friendly access via domain names on port 80.

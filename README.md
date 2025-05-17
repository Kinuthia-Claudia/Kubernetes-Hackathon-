# Kubernetes-Hackathon-
Welcome to the **Widgetario Kubernetes Hackathon**! This project showcases a complete containerized application modeled, deployed, and productionized in Kubernetes — from simple deployments to configuration, storage, Ingress, observability, and monitoring.

## Table of Contents
- [Project Overview](#-overview)
- [Project Structure](#-project-structure)
- [Project Objective](#-project-objective)
- [Technologies Needed](#-technologies-needed)
- [Prerequisites](#-prerequisites)
- [Installation & Setup](#-installation--setup)
- [Project Structure](#-project-structure)
- [Features Implemented](#-features-implemented)
- [Credits](#-credits)
- [Conclusion](#-conclusion)
- [License](#-license)
---
## OverView

This repository includes the development of the Widgetario application, a public web application for a gadget-selling company, deployed on Kubernetes as part of a Kubernetes Course Labs Hackathon.

---
##  Project Structure

The widgetario consists of
- `products-db`PostgreSQL database
- `products-api`Java Spring Boot API for products
- `stock-api` Go API managing stock levels
- `web` NET Core frontend web app
---

##  Project Objectives
This project is divided into **6 parts**, each focusing on real-world Kubernetes practices
1. **Deploying microservices**
2. **Securing configuration using Secrets & ConfigMaps**
3. **Persisting state with volumes & caching**
4. **Exposing services via Ingress & custom domains**
5. **Applying production hardening best practices**
6. **Monitoring and logging via Prometheus, Grafana & Fluentd**
---

# Technologies Needed

- **Kubernetes** (Minikube or Docker Desktop)
- **Docker**
- **kubectl**
- **PowerShell (Windows)**
- **PostgreSQL**
- **Java (Spring Boot)**
- **Helm**
- **Git**
- **Jenkins (for CI/CD)**
- **Optional - Testkube for testing**
- **.NET Core**
- **Prometheus + Grafana**
---
  
## Prerequisites
Ensure the following tools are installed on your **Windows machine** to get started 
[Docker Desktop](https://www.docker.com/products/docker-desktop/) 
[Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/) 
[PowerShell](https://learn.microsoft.com/en-us/powershell/) 
[Git](https://git-scm.com/)
[Visual Studio Code](https://code.visualstudio.com/) 
---

##  Installation & Setup
Installation Instructions
### 1. Clone the repository
1. Clone the Repository 
git clone https://github.com/your-username/widgetario-kubernetes.git

---
### Part 1 – Basic Deployment
The goal here is to get the core Widgetario application up and running inside Kubernetes using basic manifests.
We started by deploying all components using basic `Deployment` commands
'''bash 
kubectl apply -f hackathon/solution-part-1/products-db \
              -f hackathon/solution-part-1/products-api \
              -f hackathon/solution-part-1/stock-api \
              -f hackathon/solution-part-1/web
We Deployed 4 microservices.
Used Deployment and Service YAML files.

### Part 2 – Secure & Dynamic Configuration
The goal here is to Improve security and flexibility by externalizing environment variables.
Security and flexibility were added by replacing hardcoded configs with Secrets, ConfigMaps, and environment variables.

Changes that were made
1. Created a Kubernetes Secret for the PostgreSQL password.
2. Used ConfigMap to hold non-sensitive values like feature flags and URLs.
3. The .NET web app was configured to read from /app/secrets/api.json.

This code was ran to ensure all the deployed files were configured with the new code
kubectl apply -f hackathon/solution-part-2/products-db \
              -f hackathon/solution-part-2/products-api \
              -f hackathon/solution-part-2/stock-api \
              -f hackathon/solution-part-2/web
              
The out come was that  Secure configuration was enabled and  dark mode was also enabled via Widgetario__Theme=dark.

### Part 3 – Storage and Replication
The goal here is to Make the database persistent and stateful so data survives container restarts or rescheduling.
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

### Part 4 – Ingress and DNS

The goal here was to Expose the application to users with real URLs (not just NodePorts), and support routing by domain name.
We deployed an Ingress Controller  (e.g., NGINX)and exposed the services.
kubectl apply -f hackathon/solution-part-4/ingress-controller \
              -f hackathon/solution-part-4/products-db \
              -f hackathon/solution-part-4/products-api \
              -f hackathon/solution-part-4/stock-api \
              -f hackathon/solution-part-4/web
Outcome was a User-friendly access on port 80.

### Part 5 – Production Readiness
The goal here was to Make the app ready for production with resource control, security, and resilience.

What we did 
- Add liveness/readiness probes.
- Set resource requests and limits.
- Implement pod-level security.
- Graceful termination and rolling updates

Run
kubectl apply -f hackathon/solution-part-5/ingress-controller \
              -f hackathon/solution-part-5/products-db \
              -f hackathon/solution-part-5/products-api \
              -f hackathon/solution-part-5/stock-api \
              -f hackathon/solution-part-5/web
              
Now, the Kubernetes setup behaves like a real-world production cluster.

### Part 6 – Observability (Monitoring + Logging)
The goal here is to Add tools to monitor performance, health, and logs from all services.

What we did 
- Use Prometheus to collect metrics.
- Create dashboards in Grafana.
- Aggregate and ship logs via Fluent Bit.
  
Outcome  was Full observability with metrics and logs in dashboards.

### Part 7 - CI/CD
The goal here was to do  Optional enhancements to test robustness, performance, and deployment automation.
- Automate builds and deployments with Jenkins.-
- Integrate with GitHub Webhooks.
- Trigger deployments to Kubernetes using pipelines.

# Lessons Learned
Secure config handling with Secrets & ConfigMaps
StatefulSets for replicated DBs
Proper use of readiness/liveness probes
Observability with Prometheus
Real-world troubleshooting with logs 

## Project Structure 
├── hackathon/
│   ├── solution-part-1/
│   ├── solution-part-2/
│   ├── solution-part-3/
│   ├── solution-part-4/
│   ├── solution-part-5/
│   ├── solution-part-6/
├── scripts/
│   └── add-to-hosts.ps1
├── files/
│   ├── grafana-dashboard.json
│   └── kibana-dashboard.ndjson

## Credits
This project is part of the Kubernetes CourseLabs Hackathon.

 ## Conclusion
This project demonstrates real-world Kubernetes deployment strategies, security practices, and observability techniques — all deployed using Windows PowerShell. Ideal for learning and showcasing Kubernetes expertise on a Windows platform.

## License
MIT License. Free to use and adapt.

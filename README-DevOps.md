# DevOps Pipeline for Smart eCommerce Project

This document outlines the DevOps pipeline setup for the Smart eCommerce project, including Dockerization, CI/CD with GitHub Actions, Kubeflow Pipeline, and local Kubernetes deployment.

## Overview

The DevOps pipeline consists of:

1. **Dockerization** of three main components:
   - Scraping agent (Python + Selenium/BeautifulSoup)
   - ML model (Python + scikit-learn)
   - BI dashboard (Streamlit)

2. **CI/CD with GitHub Actions**:
   - Automated testing with pytest
   - Docker image builds
   - Push to Docker Hub

3. **Kubeflow Pipeline**:
   - Data preprocessing
   - Model training
   - Top-K product selection

4. **Local Kubernetes Deployment**:
   - Minikube with Docker driver
   - Kubernetes manifests for deployments and services

## Prerequisites

- Docker installed
- Minikube installed
- kubectl installed
- Python 3.9+
- Docker Hub account

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Set Up Docker Hub Secrets

For GitHub Actions to push to Docker Hub, add these secrets to your GitHub repository:

- `DOCKER_HUB_USERNAME`: Your Docker Hub username
- `DOCKER_HUB_ACCESS_TOKEN`: Your Docker Hub access token

### 3. Local Development and Testing

Build Docker images locally:

```bash
# Build scraper image
cd agent_scraping
docker build -t ecommerce-scraper .

# Build ML model image
cd ../Analyse-et-s-lection-des-Top-K-produits
docker build -t ecommerce-ml .

# Build dashboard image
cd ../LLM_pour_enrichissement-et-synthese
docker build -t ecommerce-dashboard .
```

### 4. Set Up Minikube and Kubeflow

Run the setup script:

```bash
chmod +x setup-minikube.sh
./setup-minikube.sh
```

This script will:
- Start Minikube with Docker driver
- Enable necessary addons
- Install Kubeflow Pipelines
- Create PVC for data sharing
- Deploy applications to Kubernetes

### 5. Run Kubeflow Pipeline

```bash
# Compile the pipeline
python kubeflow-pipeline.py

# Upload and run the pipeline using the Kubeflow UI
# Access the UI at the URL provided by the setup script
```

## Project Structure

```
.
├── agent_scraping/
│   ├── Dockerfile
│   ├── diverse_scraper.py
│   └── requirements.txt
├── Analyse-et-s-lection-des-Top-K-produits/
│   ├── Dockerfile
│   ├── model_training.py
│   └── requirements.txt
├── LLM_pour_enrichissement-et-synthese/
│   ├── Dockerfile
│   ├── app_streamlit.py
│   └── requirements.txt
├── .github/
│   └── workflows/
│       └── ci-cd.yml
├── k8s/
│   ├── ml-model-deployment.yaml
│   ├── dashboard-deployment.yaml
│   └── pvc.yaml
├── kubeflow-pipeline.py
└── setup-minikube.sh
```

## Troubleshooting

### Docker Issues

- **Image pull errors**: Ensure Docker Hub credentials are correct
- **Build failures**: Check Dockerfile syntax and dependencies

### Minikube Issues

- **Insufficient resources**: Increase allocated resources with `minikube config set memory 4096`
- **Driver issues**: Try alternative drivers like `virtualbox` if `docker` driver fails

### Kubeflow Issues

- **Pipeline failures**: Check component logs with `kubectl logs -n kubeflow <pod-name>`
- **UI not accessible**: Ensure port forwarding is active with `kubectl port-forward -n kubeflow svc/ml-pipeline-ui 8080:80`

## Next Steps

1. Add monitoring with Prometheus and Grafana
2. Implement automated testing for ML models
3. Set up data versioning with DVC
4. Add CI/CD for infrastructure using Terraform
# Student Performance — Azure Deployment

A complete end-to-end solution for predicting student performance, built using Python, Docker, and deployed to Azure. This repository contains everything needed to train a model, containerize the application, and deploy it in Azure infrastructure.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Architecture & Components](#architecture--components)
4. [Getting Started](#getting-started)

   * [Prerequisites](#prerequisites)
   * [Repository Structure](#repository-structure)
   * [Installation & Local Setup](#installation--local-setup)
   * [Training & Notebook](#training--notebook)
   * [Dockerization](#dockerization)
   * [Azure Deployment](#azure-deployment)
5. [Usage](#usage)
6. [Configuration & Environment Variables](#configuration--environment-variables)
7. [CI/CD & Automation](#cicd--automation)
8. [Monitoring & Logging](#monitoring--logging)
9. [Security Considerations](#security-considerations)
10. [Testing](#testing)
11. [Troubleshooting & FAQs](#troubleshooting--faqs)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgements](#acknowledgements)

---

## Project Overview

With increasing emphasis on data-driven interventions in education, this project aims to predict which students may struggle or succeed based on various features (e.g. demographic, academic history, etc.). The system encapsulates the following flow:

1. Data exploration and model training (in notebooks / scripts).
2. Packaging the model and inference logic in a Python web app (e.g., Flask or FastAPI).
3. Containerizing the app with Docker.
4. Pushing the container image to Azure Container Registry (ACR).
5. Deploying and serving the app in Azure (App Service, AKS, or equivalent).

The result is a scalable, maintainable, and production-ready student performance prediction service.

---

## Features

* **Model training & experimentation**: Jupyter notebooks and scripts to explore data, build models, and evaluate performance.
* **Web API service**: A lightweight server exposing endpoints for prediction requests (e.g., `POST /predict`).
* **Docker support**: `Dockerfile` to build the service image.
* **Azure Deployment**: Includes templates, scripts, and configuration to deploy the container into Azure services.
* **Separation of concerns**: Clear folder structure to distinguish between data, code, templates, and deployment artifacts.
* **Extensibility**: Easily swap in new models, add endpoints, or extend to more complex pipelines.

---

## Architecture & Components

Below is a high-level architecture diagram (replace or embed a visual later):

```
User → HTTP Request → Web API Container → (loads model) → Predict → Response  
                     ↑  
             (deployed on Azure via ACR, Azure App Service / AKS)  
```

Key components:

| Component                                | Purpose                                                                                     |
| ---------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Notebook / `notebook/` folder**        | Data exploration, feature engineering, baseline models, evaluation                          |
| **`src/` folder**                        | Core application logic including model loading, request handling, data preprocessing        |
| **`templates/`**                         | (Optional) HTML templates if you provide a simple UI or web interface                       |
| **`Dockerfile`**                         | Instructions to build the container image                                                   |
| **`setup.py` / `requirements.txt`**      | Python packaging and dependency management                                                  |
| **Azure deployment scripts / templates** | Infrastructure as code — e.g. ARM templates, Bicep, or scripts to deploy container to Azure |

---

## Getting Started

### Prerequisites

* Python 3.7+
* Docker Engine
* Azure CLI
* Access to an Azure subscription
* (Optional) VS Code or another IDE

### Repository Structure

```
├── notebook/
│   ├── data_exploration.ipynb
│   ├── modeling.ipynb
│   └── ...
├── src/
│   ├── app.py
│   ├── model.py
│   ├── utils.py
│   └── ...
├── templates/
│   └── index.html
├── Dockerfile
├── setup.py
├── requirements.txt
└── azure-deploy/
    ├── arm-template.json
    ├── deployment-script.sh
    └── README-azure.md
```

### Installation & Local Setup

```bash
git clone https://github.com/ALFIE-SADMAN/Student-Performance-Azure-deployment.git
cd Student-Performance-Azure-deployment

python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

(Optional):

```bash
pip install -e .
```

### Training & Notebook

* Use notebooks in `notebook/` for exploration, feature engineering, and modeling.
* Save trained artifacts (e.g., `model.pkl`) into `src/models/`.
* The API app will load and serve these models.

### Dockerization

```bash
docker build -t student-performance-app:latest .
docker run -p 8080:8080 student-performance-app:latest
```

Test with:

```bash
curl -X POST http://localhost:8080/predict \
  -H "Content-Type: application/json" \
  -d '{"feature1": value, "feature2": value}'
```

### Azure Deployment

```bash
az login
az acr create --resource-group MyResourceGroup --name MyRegistry --sku Basic
az acr login --name MyRegistry
docker tag student-performance-app:latest MyRegistry.azurecr.io/student-performance-app:latest
docker push MyRegistry.azurecr.io/student-performance-app:latest

az webapp create \
  --resource-group MyResourceGroup \
  --plan MyAppServicePlan \
  --name MyWebAppName \
  --deployment-container-image-name MyRegistry.azurecr.io/student-performance-app:latest
```

---

## Usage

Example request:

```json
{
  "age": 16,
  "study_time": 2,
  "failures": 0,
  "absences": 3,
  "G1": 12,
  "G2": 13
}
```

Response:

```json
{
  "predicted_score": 14.2,
  "class_label": "Pass",
  "confidence": 0.87
}
```

---

## Configuration & Environment Variables

| ENV var                    | Description       | Example value       |
| -------------------------- | ----------------- | ------------------- |
| `MODEL_PATH`               | Path to model     | `models/model.pkl`  |
| `SCALER_PATH`              | Preprocessor path | `models/scaler.pkl` |
| `PORT`                     | App port          | `8080`              |
| `LOG_LEVEL`                | Logging level     | `INFO`              |
| `AZURE_STORAGE_CONNECTION` | Azure storage key | `...`               |

---

## CI/CD & Automation

* Use GitHub Actions / Azure Pipelines for building and deploying.
* Automate Docker builds, ACR pushes, and Web App/Kubernetes deployment.
* Infrastructure as Code recommended (ARM / Bicep / Terraform).

---

## Monitoring & Logging

* Use **Azure Application Insights** for telemetry.
* Log API requests, predictions, errors.
* Enable alerts for failures or latency spikes.
* Store logs in **Azure Log Analytics**.

---

## Security Considerations

* Input validation and sanitization.
* Authentication (API key, OAuth, Azure AD).
* Secrets managed in **Azure Key Vault**.
* HTTPS/TLS enforced.
* Regular container vulnerability scans.

---

## Testing

```bash
pytest tests/
```

* Unit tests for preprocessing and inference.
* Integration tests for containerized app.
* Postman/Azure Test Plans for deployed API.

---

## Troubleshooting & FAQs

| Issue                | Fix                                  |
| -------------------- | ------------------------------------ |
| 500 error            | Check logs, model path incorrect     |
| Cannot push ACR      | Ensure `az acr login` ran            |
| Web app fails        | Check container logs in Azure Portal |
| Env vars not working | Verify App Settings                  |
| Low accuracy         | Improve preprocessing & model tuning |

---

## Contributing

1. Fork the repo.
2. Create a branch (`feature/new-feature`).
3. Commit with clear messages.
4. Add/update tests.
5. Submit PR.

---

## License

```text
MIT License
Copyright (c) 2025 ALFIE-SADMAN
```

---

## Acknowledgements

* Libraries: scikit-learn, pandas, Flask/FastAPI.
* Azure documentation and tutorials.
* Open educational data sources.


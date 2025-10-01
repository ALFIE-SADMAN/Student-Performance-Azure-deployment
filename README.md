# Student Performance — Azure Deployment

[![Build Status](https://img.shields.io/github/actions/workflow/status/ALFIE-SADMAN/Student-Performance-Azure-deployment/main_studentperformancecheck.yml?branch=main)](https://github.com/ALFIE-SADMAN/Student-Performance-Azure-deployment/actions)
[![Docker](https://img.shields.io/badge/Docker-ready-blue.svg)](https://www.docker.com/)
[![Azure](https://img.shields.io/badge/Deployed%20on-Azure-blue)](https://azure.microsoft.com/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-brightgreen)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A complete end-to-end solution for predicting student performance, built using **Python, Flask, Docker, and Azure**. This repository demonstrates the full lifecycle of a machine learning project: from exploratory data analysis and model training, to containerization and deployment in the cloud.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Project Structure](#project-structure)
4. [Getting Started](#getting-started)

   * [Prerequisites](#prerequisites)
   * [Installation & Local Setup](#installation--local-setup)
   * [Training & Notebooks](#training--notebooks)
   * [Running the Flask App](#running-the-flask-app)
   * [Dockerization](#dockerization)
   * [Azure Deployment](#azure-deployment)
5. [Usage](#usage)
6. [Configuration](#configuration)
7. [CI/CD](#cicd)
8. [Monitoring & Logging](#monitoring--logging)
9. [Security](#security)
10. [Testing](#testing)
11. [Troubleshooting](#troubleshooting)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgements](#acknowledgements)

---

## Project Overview

This project predicts student performance based on academic and demographic data. The workflow includes:

1. Data exploration and preprocessing in Jupyter notebooks.
2. Training ML models (e.g., CatBoost, Scikit-learn pipelines).
3. Saving trained artifacts (model + preprocessor).
4. Serving predictions via a Flask web application.
5. Containerizing with Docker for reproducibility.
6. Deploying on Azure App Service using CI/CD workflows.

The final product is a **scalable prediction API** with a simple front-end interface.

---

## Features

* **Exploratory Data Analysis (EDA)**: Insights into student data with Jupyter notebooks.
* **Model Training**: Scripts for ingestion, transformation, training, and evaluation.
* **Reusable Pipelines**: Modular code for preprocessing and prediction.
* **Flask Web App**: Simple UI (`templates/home.html`, `index.html`) for user inputs.
* **Dockerized Deployment**: Easy containerization using the included `Dockerfile`.
* **Azure CI/CD**: GitHub Actions workflow (`.github/workflows/main_studentperformancecheck.yml`) for automated deployment.
* **Logging & Error Handling**: Centralized logging and custom exception handling.

---

## Project Structure

```
Student-Performance-Azure-deployment/
├── Dockerfile                  # Container build instructions
├── README.md                   # Project documentation
├── app.py                      # Flask application entrypoint
├── requirements.txt            # Python dependencies
├── setup.py                    # Project setup script
├── .github/
│   └── workflows/
│       └── main_studentperformancecheck.yml   # CI/CD workflow
├── artifacts/                  # Saved ML artifacts
│   ├── data.csv
│   ├── model.pkl
│   ├── preprocessor.pkl
│   ├── train.csv
│   └── test.csv
├── notebook/                   # Jupyter notebooks for exploration
│   ├── 1 . EDA STUDENT PERFORMANCE .ipynb
│   ├── 2. MODEL TRAINING.ipynb
│   └── data/stud.csv
├── src/                        # Core project code
│   ├── exception.py             # Custom exceptions
│   ├── logger.py                # Logging utilities
│   ├── utils.py                 # Helper functions
│   ├── components/              # Data & model pipelines
│   │   ├── data_ingestion.py
│   │   ├── data_transformation.py
│   │   └── model_trainer.py
│   └── pipeline/
│       └── predict_pipeline.py  # Prediction logic
└── templates/                  # Flask templates
    ├── home.html
    └── index.html
```

---

## Getting Started

### Prerequisites

* Python 3.8+
* Docker
* Azure CLI
* Git

### Installation & Local Setup

```bash
git clone https://github.com/ALFIE-SADMAN/Student-Performance-Azure-deployment.git
cd Student-Performance-Azure-deployment

python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### Training & Notebooks

* Use notebooks in `notebook/` for EDA and model training.
* Artifacts (`model.pkl`, `preprocessor.pkl`) will be saved in `artifacts/`.

### Running the Flask App

```bash
python app.py
```

Visit: [http://127.0.0.1:5000](http://127.0.0.1:5000)

### Dockerization

```bash
docker build -t student-performance-app .
docker run -p 8080:8080 student-performance-app
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

* Web UI: Fill in student features in the form (from `home.html`) to get predictions.
* API: Use `POST /predict` with JSON input. Example:

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

## Configuration

| ENV var       | Description            |
| ------------- | ---------------------- |
| `MODEL_PATH`  | Path to model artifact |
| `SCALER_PATH` | Path to preprocessor   |
| `PORT`        | Flask app port         |
| `LOG_LEVEL`   | Logging verbosity      |

---

## CI/CD

* GitHub Actions workflow builds, tests, and deploys Docker image.
* Workflow file: `.github/workflows/main_studentperformancecheck.yml`.

---

## Monitoring & Logging

* Logs captured using `logger.py`.
* Azure Application Insights recommended for production monitoring.

---

## Security

* Sanitize all inputs.
* Store secrets (e.g., Azure creds) in **Key Vault**.
* Enforce HTTPS for all endpoints.

---

## Testing

```bash
pytest tests/
```

* Unit tests for `src/components` and `src/pipeline`.
* Integration tests for Flask API.

---

## Troubleshooting

| Issue                   | Fix                                                               |
| ----------------------- | ----------------------------------------------------------------- |
| 500 Error on `/predict` | Check if `model.pkl` and `preprocessor.pkl` exist in `artifacts/` |
| Docker build fails      | Verify Python version and dependencies                            |
| Azure deploy fails      | Ensure ACR and App Service exist, check credentials               |

---

## Contributing

1. Fork this repo.
2. Create a feature branch (`feature/new-feature`).
3. Add tests and documentation.
4. Submit a PR.

---

## License

```
MIT License
Copyright (c) 2025 ALFIE-SADMAN
```

---

## Acknowledgements

* **scikit-learn**, **pandas**, **CatBoost** for ML.
* **Flask** for the web app.
* **Azure App Service** for deployment.
* GitHub Actions for CI/CD.

# Python AI/ML Skill

## Purpose
Expert Python development for AI, Machine Learning, Data Science, and automation. Covers the entire ML lifecycle from data preprocessing to model deployment.

## Capabilities

### AI/ML Frameworks
- **Deep Learning**: TensorFlow, PyTorch, Keras, JAX
- **ML Libraries**: scikit-learn, XGBoost, LightGBM, CatBoost
- **NLP**: transformers (HuggingFace), spaCy, NLTK, gensim
- **Computer Vision**: OpenCV, torchvision, Pillow
- **Reinforcement Learning**: stable-baselines3, gymnasium

### Data Science
- **Data Manipulation**: pandas, polars, NumPy
- **Visualization**: matplotlib, seaborn, plotly, Altair
- **Statistics**: scipy, statsmodels
- **Feature Engineering**: feature-engine, category_encoders

### MLOps & Production
- **Experiment Tracking**: MLflow, Weights & Biases, Neptune
- **Model Serving**: FastAPI, Flask, BentoML, TensorFlow Serving
- **Pipeline Orchestration**: Kubeflow, Airflow, Prefect
- **Model Optimization**: ONNX, TensorRT, quantization, pruning
- **Monitoring**: Evidently AI, Great Expectations

### Automation & Scripting
- **Web Scraping**: BeautifulSoup, Scrapy, Selenium, Playwright
- **Task Automation**: schedule, Celery, RQ
- **API Development**: FastAPI, Flask, Django REST Framework
- **CLI Tools**: Typer, Click, argparse

## Code Patterns

### FastAPI ML Service
```python
from fastapi import FastAPI
from pydantic import BaseModel
import numpy as np

app = FastAPI()

class PredictionRequest(BaseModel):
    features: list[float]

class PredictionResponse(BaseModel):
    prediction: float
    confidence: float

@app.post("/predict", response_model=PredictionResponse)
async def predict(request: PredictionRequest):
    features = np.array(request.features).reshape(1, -1)
    prediction = model.predict(features)[0]
    confidence = model.predict_proba(features).max()
    return PredictionResponse(prediction=float(prediction), confidence=float(confidence))
```

### PyTorch Training Loop
```python
import torch
import torch.nn as nn
from torch.utils.data import DataLoader

def train_epoch(model, loader, optimizer, criterion):
    model.train()
    total_loss = 0
    for batch in loader:
        optimizer.zero_grad()
        outputs = model(batch['input'])
        loss = criterion(outputs, batch['target'])
        loss.backward()
        optimizer.step()
        total_loss += loss.item()
    return total_loss / len(loader)
```

### Data Pipeline
```python
import pandas as pd
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier

pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('classifier', RandomForestClassifier(n_estimators=100))
])

pipeline.fit(X_train, y_train)
predictions = pipeline.predict(X_test)
```

## Best Practices
- Use virtual environments (venv, conda, poetry)
- Version control data and models with DVC
- Log experiments and metrics
- Write unit tests for data transformations
- Use type hints and docstrings
- Profile code for bottlenecks (cProfile, line_profiler)
- Use async I/O for I/O-bound operations

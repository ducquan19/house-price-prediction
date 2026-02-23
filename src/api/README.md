# 🚀 House Price Prediction API

A production-ready FastAPI service for predicting house prices using a trained XGBoost machine learning model.

[![FastAPI](https://img.shields.io/badge/FastAPI-0.129.2-009688.svg)](https://fastapi.tiangolo.com/)
[![Python 3.12+](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/)
[![uv](https://img.shields.io/badge/uv-package%20manager-blue.svg)](https://github.com/astral-sh/uv)
[![Uvicorn](https://img.shields.io/badge/Uvicorn-0.41.0-purple.svg)](https://www.uvicorn.org/)

---

## 📖 Overview

This API provides RESTful endpoints for house price prediction with features including:

- **Single & Batch Predictions**: Predict one or multiple houses at once
- **Automatic Validation**: Pydantic models ensure data integrity
- **Interactive Documentation**: Built-in Swagger UI and ReDoc
- **Health Checks**: Monitor service status and model availability
- **Model Information**: Access performance metrics and metadata
- **Production Ready**: Docker support with health monitoring

---

## � Installation

Before running the API, ensure you have the dependencies installed.

### Prerequisites

- **Python 3.12+**
- **uv** package manager ([installation guide](https://github.com/astral-sh/uv))
- **Trained model**: Run `uv run python train.py` from project root first

### Install Dependencies

From the project root:

```bash
# Install all dependencies
uv pip install -e .

# Or sync with lock file
uv sync
```

### Verify Model Files

Ensure these files exist:

- `src/models/best_pipeline.joblib`
- `src/config/best_model_config.json`

If not, train the model first:

```bash
uv run python train.py
```

---

## 🚀 Quick Start

### Running Locally

From the project root:

```bash
# Option 1: Using the run script with uv
uv run python src/api/run_api.py

# Option 2: Using uvicorn directly with uv
uv run uvicorn src.api.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at **http://localhost:8000**

### Running with Docker

```bash
# Build the image
docker build -f src/api/Dockerfile -t house-price-api .

# Run the container
docker run -p 8000:8000 house-price-api
```

### Running with Docker Compose

```bash
cd deployments/api
docker compose up -d
```

This will start the API, frontend, and MLflow services together.

---

## 📚 API Documentation

### Interactive Documentation

Once the API is running, access the interactive documentation:

- **Swagger UI**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **ReDoc**: [http://localhost:8000/redoc](http://localhost:8000/redoc)
- **OpenAPI Schema**: [http://localhost:8000/openapi.json](http://localhost:8000/openapi.json)

---

## 🔌 API Endpoints

### 1. Health Check

Check if the API service and model are ready.

**Endpoint:** `GET /health`

**Example:**

```bash
curl http://localhost:8000/health
```

**Response:**

```json
{
  "status": "healthy",
  "model_loaded": true,
  "model_path": "/app/src/models/best_pipeline.joblib"
}
```

---

### 2. Model Information

Get detailed information about the loaded model including performance metrics.

**Endpoint:** `GET /model/info`

**Example:**

```bash
curl http://localhost:8000/model/info
```

**Response:**

```json
{
  "model_type": "XGBoost",
  "model_version": "1.0",
  "performance": {
    "cv_rmse_mean": 25259.42,
    "cv_rmse_std": 3479.64,
    "test_rmse": 24608.89,
    "test_r2": 0.921
  },
  "features_count": 18,
  "trained_date": "2025-10-26"
}
```

---

### 3. Single House Prediction

Predict the price for a single house.

**Endpoint:** `POST /predict`

**Request Body:**

All fields are optional - the model handles missing values automatically.

```json
{
  "MSSubClass": 60,
  "LotArea": 8450,
  "OverallQual": 7,
  "OverallCond": 5,
  "YearBuilt": 2003,
  "YearRemodAdd": 2003,
  "GrLivArea": 1710,
  "FullBath": 2,
  "HalfBath": 1,
  "BedroomAbvGr": 3,
  "TotRmsAbvGrd": 8,
  "Fireplaces": 0,
  "GarageCars": 2,
  "GarageArea": 548
}
```

**Example with curl:**

```bash
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{
    "OverallQual": 7,
    "GrLivArea": 1710,
    "YearBuilt": 2003,
    "GarageCars": 2,
    "TotalBsmtSF": 856
  }'
```

**Example with Python:**

```python
import requests

house_data = {
    "OverallQual": 7,
    "GrLivArea": 1710,
    "YearBuilt": 2003,
    "GarageCars": 2
}

response = requests.post(
    "http://localhost:8000/predict",
    json=house_data
)

print(response.json())
```

**Response:**

```json
{
  "predicted_price": 208500.0,
  "prediction_log": 12.247,
  "model_version": "1.0"
}
```

---

### 4. Batch Prediction

Predict prices for multiple houses in one request.

**Endpoint:** `POST /predict/batch`

**Request Body:**

```json
{
  "houses": [
    {
      "OverallQual": 7,
      "GrLivArea": 1710,
      "YearBuilt": 2003
    },
    {
      "OverallQual": 5,
      "GrLivArea": 1200,
      "YearBuilt": 1985
    },
    {
      "OverallQual": 8,
      "GrLivArea": 2000,
      "YearBuilt": 2010
    }
  ]
}
```

**Example with curl:**

```bash
curl -X POST "http://localhost:8000/predict/batch" \
  -H "Content-Type: application/json" \
  -d '{
    "houses": [
      {"OverallQual": 7, "GrLivArea": 1710, "YearBuilt": 2003},
      {"OverallQual": 5, "GrLivArea": 1200, "YearBuilt": 1985}
    ]
  }'
```

**Example with Python:**

```python
import requests

houses_data = {
    "houses": [
        {"OverallQual": 7, "GrLivArea": 1710, "YearBuilt": 2003},
        {"OverallQual": 5, "GrLivArea": 1200, "YearBuilt": 1985},
        {"OverallQual": 8, "GrLivArea": 2000, "YearBuilt": 2010}
    ]
}

response = requests.post(
    "http://localhost:8000/predict/batch",
    json=houses_data
)

predictions = response.json()
for i, pred in enumerate(predictions["predictions"]):
    print(f"House {i+1}: ${pred:,.2f}")
```

**Response:**

```json
{
  "predictions": [208500.0, 145000.0, 265000.0],
  "count": 3,
  "model_version": "1.0"
}
```

---

## 📂 Project Structure

```
src/api/
├── __init__.py              # Package initialization
├── main.py                  # FastAPI application & endpoints
├── models.py                # Pydantic models for validation
├── inference.py             # Core inference logic & CLI tool
├── run_api.py               # API server startup script
├── Dockerfile               # Container image definition
└── README.md                # This file
```

---

## ⚙️ Configuration

### Environment Variables

| Variable     | Default                           | Description                   |
| ------------ | --------------------------------- | ----------------------------- |
| `MODEL_PATH` | `src/models/best_pipeline.joblib` | Path to trained model         |
| `HOST`       | `0.0.0.0`                         | API host address              |
| `PORT`       | `8000`                            | API port number               |
| `RELOAD`     | `False`                           | Enable auto-reload (dev mode) |

### Model Requirements

The API requires these files to be present:

- **Model Pipeline**: `src/models/best_pipeline.joblib`
- **Model Config**: `src/config/best_model_config.json`

Ensure you've trained the model first:

```bash
python train.py
```

---

## 🧪 Testing

### Automated Test Script

```bash
uv run python src/api/test_api.py
```

This will test all endpoints and verify responses.

### Manual Testing

**Test health endpoint:**

```bash
curl http://localhost:8000/health
```

**Test single prediction:**

```bash
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"OverallQual": 7, "GrLivArea": 1710}'
```

**Test with invalid data:**

```bash
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"OverallQual": "invalid"}'
```

---

## 🐳 Docker Deployment

### Building the Image

From project root:

```bash
docker build -f src/api/Dockerfile -t house-price-api:latest .
```

### Running the Container

**Basic run:**

```bash
docker run --rm -p 8000:8000 house-price-api:latest
```

**With mounted volumes (for development):**

```bash
docker run --rm -p 8000:8000 \
  -v "$PWD/src/models:/app/src/models:ro" \
  -v "$PWD/src/config:/app/src/config:ro" \
  house-price-api:latest
```

**With custom environment variables:**

```bash
docker run --rm -p 8000:8000 \
  -e MODEL_PATH=/app/src/models/best_pipeline.joblib \
  -e PORT=8080 \
  house-price-api:latest
```

### Health Check

Docker health checks are configured in the Dockerfile:

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD python -c "import requests; requests.get('http://localhost:8000/health').raise_for_status()"
```

---

## 🔍 CLI Inference Tool

The `inference.py` module can also be used as a command-line tool for batch predictions.

### Usage

```bash
uv run python src/api/inference.py <input_csv> --output <output_csv>
```

### Example

```bash
uv run python src/api/inference.py data/raw/test_houses.csv --output predictions.csv
```

**Input CSV format:**

```csv
OverallQual,GrLivArea,YearBuilt,GarageCars
7,1710,2003,2
5,1200,1985,1
8,2000,2010,2
```

**Output CSV format:**

```csv
predicted_price
208500.0
145000.0
265000.0
```

---

## 🛠️ Development

### Running in Development Mode

```bash
uv run uvicorn src.api.main:app --reload --host 0.0.0.0 --port 8000
```

The `--reload` flag enables hot-reloading when code changes.

### Adding New Endpoints

1. Define Pydantic models in [models.py](models.py)
2. Add endpoint logic in [main.py](main.py)
3. Update this README with documentation
4. Add tests in `test_api.py`

### Code Style

Follow PEP 8 and use type hints:

```python
from fastapi import FastAPI
from typing import Dict, Any

@app.get("/example")
async def example_endpoint() -> Dict[str, Any]:
    return {"message": "example"}
```

---

## 📊 Performance

- **Cold start**: ~2-3 seconds (model loading)
- **Prediction latency**: ~10-50ms per house
- **Throughput**: ~100-200 requests/second
- **Memory usage**: ~300MB with model loaded

---

## 🔒 Security Considerations

For production deployment:

- [ ] Add authentication (API keys, OAuth2, JWT)
- [ ] Implement rate limiting
- [ ] Enable HTTPS/TLS
- [ ] Add input sanitization
- [ ] Set up CORS policies
- [ ] Monitor and log requests
- [ ] Implement request timeouts

---

## 🐛 Troubleshooting

### Model Not Found Error

**Error:** `FileNotFoundError: Model file not found`

**Solution:** Train the model first:

```bash
uv run python train.py
```

### Port Already in Use

**Error:** `Error: [Errno 98] Address already in use`

**Solution:** Use a different port or kill the existing process:

```bash
lsof -ti:8000 | xargs kill -9
uv run uvicorn src.api.main:app --port 8001
```

### Import Errors

**Error:** `ModuleNotFoundError: No module named 'src'`

**Solution:** Run from project root and ensure dependencies are installed:

```bash
uv pip install -e .
uv run python src/api/run_api.py
```

---

## 📝 API Response Codes

| Code | Description                            |
| ---- | -------------------------------------- |
| 200  | Success                                |
| 422  | Validation Error (invalid input)       |
| 500  | Internal Server Error                  |
| 503  | Service Unavailable (model not loaded) |

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Test your changes thoroughly
2. Update this README if adding new endpoints
3. Follow existing code style
4. Add appropriate error handling

---

## 📄 License

Part of the House Price Prediction System - MIT License

---

## 📚 Additional Resources

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [Uvicorn Documentation](https://www.uvicorn.org/)
- [Main Project README](../../README.md)

---

<div align="center">

**Built with FastAPI ⚡**

</div>

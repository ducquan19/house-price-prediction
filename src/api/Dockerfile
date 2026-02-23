FROM python:3.12-slim

# Prevent Python from writing .pyc files and buffering stdout/stderr
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_SYSTEM_PYTHON=1

WORKDIR /app

# System deps (build essentials for some libs like numpy/pandas)
RUN apt-get update && \
    apt-get install -y --no-install-recommends build-essential curl wget && \
    rm -rf /var/lib/apt/lists/*

# Install uv
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.cargo/bin:$PATH"

# Copy project files
COPY pyproject.toml /app/pyproject.toml

# Install Python dependencies using uv
RUN uv pip install --system -e .

# Copy only necessary project files
COPY src /app/src
COPY data/raw/train-house-prices-advanced-regression-techniques.csv /app/data/raw/train-house-prices-advanced-regression-techniques.csv

# Expose API port
EXPOSE 8000

# Healthcheck (optional but helpful)
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:8000/health || exit 1

# Start FastAPI with uvicorn
CMD ["uvicorn", "src.api.main:app", "--host", "0.0.0.0", "--port", "8000"]

FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_SYSTEM_PYTHON=1

WORKDIR /app

RUN apt-get update && \
    apt-get install -y --no-install-recommends build-essential curl && \
    rm -rf /var/lib/apt/lists/*

# Install uv
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.cargo/bin:$PATH"

# Copy project files
COPY pyproject.toml /app/pyproject.toml

# Install Python dependencies using uv
RUN uv pip install --system -e .

# Copy app source
COPY src /app/src

# Expose Streamlit default port
EXPOSE 8501

# Streamlit config to allow headless and external access
ENV STREAMLIT_BROWSER_GATHER_USAGE_STATS=false \
    STREAMLIT_SERVER_HEADLESS=true \
    STREAMLIT_SERVER_ADDRESS=0.0.0.0 \
    STREAMLIT_SERVER_PORT=8501

# At runtime, set API_URL to point to the API container or host
ENV API_URL="http://localhost:8000"

CMD ["streamlit", "run", "src/frontend/app.py"]

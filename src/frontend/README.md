# 🎨 House Price Prediction Frontend

An interactive Streamlit web application for predicting house prices with an intuitive user interface.

[![Streamlit](https://img.shields.io/badge/Streamlit-1.54.0-FF4B4B.svg)](https://streamlit.io/)
[![Python 3.12+](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/)
[![uv](https://img.shields.io/badge/uv-package%20manager-blue.svg)](https://github.com/astral-sh/uv)

---

## 📖 Overview

This Streamlit application provides a user-friendly interface for the House Price Prediction API with features including:

- **Interactive Input Forms**: Easy-to-use sliders and dropdowns for house features
- **Real-time Predictions**: Instant price predictions via API integration
- **Visual Feedback**: Clean, modern UI with responsive design
- **Form Validation**: Input validation and error handling
- **Multilingual Labels**: Descriptive labels for categorical features
- **Responsive Design**: Works on desktop and mobile devices

---

## � Installation

Before running the frontend, ensure you have the dependencies installed.

### Prerequisites

- **Python 3.12+**
- **uv** package manager ([installation guide](https://github.com/astral-sh/uv))
- **API Server**: The backend API must be running (see [API README](../api/README.md))

### Install Dependencies

From the project root:

```bash
# Install all dependencies
uv pip install -e .

# Or sync with lock file
uv sync
```

### Start the API Server

The frontend requires the API to be running. In a separate terminal:

```bash
uv run python src/api/run_api.py
```

---

## 🚀 Quick Start

### Running Locally

From the project root:

```bash
# Start the Streamlit app
uv run streamlit run src/frontend/app.py
```

The app will open automatically in your browser at **http://localhost:8501**

### Running with Docker

```bash
# Build the image
docker build -f src/frontend/Dockerfile -t house-price-frontend .

# Run the container
docker run -p 8501:8501 \
  -e API_URL=http://localhost:8000 \
  house-price-frontend
```

### Running with Docker Compose

```bash
cd deployments/api
docker compose up -d
```

This will start the frontend along with the API and MLflow services.

---

## 🎯 Features

### 1. House Feature Input

The app provides intuitive inputs for key house features:

**Quality & Condition:**

- Overall Quality (1-10 scale)
- Overall Condition (1-10 scale)
- External Quality (Excellent, Good, Average)
- Kitchen Quality (Excellent, Good, Average)

**Size & Area:**

- Ground Living Area (sq ft)
- Total Basement Area (sq ft)
- Lot Area (sq ft)
- Garage Area (sq ft)

**Rooms & Bathrooms:**

- Bedrooms Above Ground
- Full Bathrooms
- Half Bathrooms
- Total Rooms Above Ground

**Age & Style:**

- Year Built
- Year Remodeled
- House Style (1 Story, 2 Story, etc.)
- Number of Fireplaces

**Location & Type:**

- Neighborhood
- MS Zoning
- Building Type

**Utilities:**

- Garage Cars
- Central Air Conditioning

### 2. Prediction Display

After submitting the form, the app displays:

- **Predicted Price**: Large, prominent display of the estimated house price
- **Price Formatting**: Properly formatted with currency symbols and commas
- **API Response Time**: How long the prediction took
- **Error Handling**: Clear error messages if prediction fails

### 3. User Experience

- **Reset Button**: Clear all inputs and start over
- **Form Validation**: Ensures all inputs are valid before submission
- **Loading States**: Visual feedback during API calls
- **Responsive Layout**: Adapts to different screen sizes

---

## ⚙️ Configuration

### Environment Variables

| Variable                   | Default                 | Description                |
| -------------------------- | ----------------------- | -------------------------- |
| `API_URL`                  | `http://localhost:8000` | URL of the prediction API  |
| `STREAMLIT_SERVER_PORT`    | `8501`                  | Port for the Streamlit app |
| `STREAMLIT_SERVER_ADDRESS` | `0.0.0.0`               | Server address             |

### API Integration

The frontend communicates with the FastAPI backend through HTTP requests:

```python
API_URL = os.getenv("API_URL", "http://localhost:8000")

response = requests.post(
    f"{API_URL}/predict",
    json=house_data
)
```

Ensure the API is running before using the frontend.

---

## 📂 Project Structure

```
src/frontend/
├── app.py                   # Main Streamlit application
├── Dockerfile               # Container image definition
└── README.md                # This file
```

---

## 🎨 User Interface

### Layout

The app uses a clean, modern layout:

```
┌─────────────────────────────────────┐
│      🏠 House Price Prediction      │
│                                     │
│  Enter house details below:         │
│                                     │
│  ┌─────────────┐  ┌──────────────┐ │
│  │ Overall     │  │ Living Area  │ │
│  │ Quality: 7  │  │ 1710 sq ft   │ │
│  └─────────────┘  └──────────────┘ │
│                                     │
│  [More input fields...]             │
│                                     │
│  [🔮 Predict Price]  [🔄 Reset]    │
│                                     │
│  ┌─────────────────────────────────┐│
│  │  Predicted Price: $208,500      ││
│  └─────────────────────────────────┘│
└─────────────────────────────────────┘
```

### Input Groups

Inputs are organized into logical sections:

1. **Quality & Condition** - Overall ratings
2. **Size & Dimensions** - Square footages
3. **Rooms & Spaces** - Room counts
4. **Year & Style** - Construction details
5. **Location & Type** - Categorical features

---

## 🧪 Testing

### Manual Testing

1. **Start the app:**

   ```bash
   uv run streamlit run src/frontend/app.py
   ```

2. **Test basic prediction:**
   - Enter values for key fields
   - Click "Predict Price"
   - Verify prediction displays

3. **Test edge cases:**
   - Leave optional fields empty
   - Enter extreme values
   - Test with different combinations

4. **Test error handling:**
   - Stop the API server
   - Try to make a prediction
   - Verify error message displays

### API Connection Test

```python
import requests
import os

API_URL = os.getenv("API_URL", "http://localhost:8000")

# Test health endpoint
try:
    response = requests.get(f"{API_URL}/health", timeout=5)
    print(f"API Status: {response.status_code}")
except Exception as e:
    print(f"API Error: {e}")
```

---

## 🐳 Docker Deployment

### Building the Image

From project root:

```bash
docker build -f src/frontend/Dockerfile -t house-price-frontend:latest .
```

### Running the Container

**Basic run:**

```bash
docker run --rm -p 8501:8501 house-price-frontend:latest
```

**With custom API URL:**

```bash
docker run --rm -p 8501:8501 \
  -e API_URL=http://api:8000 \
  house-price-frontend:latest
```

**With Docker Compose network:**

```bash
docker run --rm -p 8501:8501 \
  --network house-pricing-network \
  -e API_URL=http://api:8000 \
  house-price-frontend:latest
```

---

## 🎨 Customization

### Styling

Streamlit allows custom CSS:

```python
st.markdown("""
    <style>
    .big-font {
        font-size: 30px !important;
        font-weight: bold;
    }
    </style>
    """, unsafe_allow_html=True)

st.markdown('<p class="big-font">$208,500</p>', unsafe_allow_html=True)
```

### Adding New Fields

To add a new input field:

1. **Add input widget in app.py:**

   ```python
   new_field = st.number_input("New Field", min_value=0, max_value=100)
   ```

2. **Include in house_data dict:**

   ```python
   house_data = {
       # ... existing fields
       "NewField": new_field
   }
   ```

3. **Update README** with new field documentation

---

## 🛠️ Development

### Running in Development Mode

```bash
# Enable auto-reload on file changes
uv run streamlit run src/frontend/app.py --server.runOnSave true
```

### Debugging

Enable debug mode in Streamlit:

```bash
uv run streamlit run src/frontend/app.py --logger.level=debug
```

Check Streamlit logs:

```bash
# View logs in the terminal
# Logs are printed to stdout by default
```

### Code Style

Follow PEP 8 and Streamlit best practices:

```python
import streamlit as st

# Use st.cache_data for expensive computations
@st.cache_data
def load_data():
    return expensive_operation()

# Use session state for persistent data
if 'prediction' not in st.session_state:
    st.session_state.prediction = None
```

---

## 📊 Performance

- **Initial Load**: ~1-2 seconds
- **Prediction Time**: ~50-200ms (depends on API)
- **Memory Usage**: ~100-150MB
- **Concurrent Users**: Supports 10+ concurrent users

---

## 🔒 Security Considerations

For production deployment:

- [ ] Use HTTPS for API communication
- [ ] Implement rate limiting on the API
- [ ] Validate all user inputs
- [ ] Set appropriate CORS headers
- [ ] Add authentication if needed
- [ ] Monitor for unusual traffic patterns

---

## 🐛 Troubleshooting

### API Connection Error

**Error:** `Connection refused` or `API not responding`

**Solution:**

1. Verify API is running: `curl http://localhost:8000/health`
2. Check API_URL environment variable
3. Ensure correct port and host

### Port Already in Use

**Error:** `Address already in use: 8501`

**Solution:**

```bash
# Use a different port
uv run streamlit run src/frontend/app.py --server.port 8502
```

### Module Import Errors

**Error:** `ModuleNotFoundError: No module named 'streamlit'`

**Solution:**

```bash
uv pip install -e .
```

### Slow Response Times

**Symptoms:** App feels sluggish, predictions take too long

**Solutions:**

- Check API server performance
- Verify network connectivity
- Increase API timeout
- Use caching for repeated requests

---

## 📱 Mobile Support

The app is responsive and works on mobile devices:

- Touch-friendly input controls
- Responsive layout adapts to screen size
- Mobile-optimized sliders and buttons

Test on mobile:

```bash
# Access from mobile device on same network
# Use computer's IP address
uv run streamlit run src/frontend/app.py --server.address 0.0.0.0
# Then visit: http://YOUR_IP:8501
```

---

## 🌐 Deployment Options

### Streamlit Cloud

Deploy to Streamlit Cloud for free:

1. Push code to GitHub
2. Connect repository to [Streamlit Cloud](https://streamlit.io/cloud)
3. Set environment variables (API_URL)
4. Deploy!

### Heroku

Deploy to Heroku:

1. Create `Procfile`:

   ```
   web: streamlit run src/frontend/app.py --server.port $PORT
   ```

2. Deploy:
   ```bash
   heroku create
   git push heroku main
   ```

### AWS/GCP/Azure

Use the provided Dockerfile for cloud deployment:

```bash
# Build and push to container registry
docker build -t your-registry/house-price-frontend .
docker push your-registry/house-price-frontend

# Deploy to cloud service
# (Follow cloud provider's container deployment guide)
```

---

## 🤝 Contributing

Contributions welcome! To contribute:

1. Test UI changes on multiple browsers
2. Ensure responsive design works
3. Update this README for new features
4. Follow Streamlit best practices

---

## 📄 License

Part of the House Price Prediction System - MIT License

---

## 📚 Additional Resources

- [Streamlit Documentation](https://docs.streamlit.io/)
- [Streamlit Components](https://streamlit.io/components)
- [Streamlit Gallery](https://streamlit.io/gallery)
- [Main Project README](../../README.md)
- [API README](../api/README.md)

---

## 💡 Tips & Tricks

### Improving User Experience

1. **Add tooltips:**

   ```python
   st.slider("Overall Quality", help="Rate the overall quality (1-10)")
   ```

2. **Use columns for layout:**

   ```python
   col1, col2 = st.columns(2)
   with col1:
       st.number_input("Field 1")
   with col2:
       st.number_input("Field 2")
   ```

3. **Add loading spinners:**

   ```python
   with st.spinner("Predicting..."):
       prediction = make_prediction()
   ```

4. **Show success messages:**
   ```python
   st.success("Prediction successful!")
   st.balloons()  # Celebration animation!
   ```

---

<div align="center">

**Built with Streamlit 🎈**

[🏠 Back to Main README](../../README.md) | [🚀 API Documentation](../api/README.md)

</div>

# Calorify - Setup and Run Guide

## Project Overview
Calorify is an AI-powered nutrition health tracker with multiple services:
- **Food Detection App** (Port 5001): Main Flask app with YOLO for food detection
- **Node.js Backend** (Port 3001): User authentication and calorie tracking
- **Medical Report Summarizer** (Port 5003): Summarizes medical reports using Gemini API
- **Chatbot** (Port 5002): AI chatbot for dietary advice

---

## Prerequisites

1. **Python 3.7+** (Virtual environment already exists in `env/`)
2. **Node.js and npm** (for the backend API)
3. **MongoDB** (for user data storage)
4. **Tesseract OCR** (for medical report processing)
   - Download from: https://github.com/UB-Mannheim/tesseract/wiki
   - Windows: Install to `C:/Program Files/Tesseract-OCR/tesseract.exe`

---

## Step 1: Python Dependencies

Activate the virtual environment and install Python packages:

```bash
# Windows
env\Scripts\activate

# Install required packages
pip install flask flask-cors opencv-python ultralytics torch torchvision pytesseract pdfminer.six pillow openai google-generativeai
```

**Note:** The project doesn't have a `requirements.txt`, so install these manually or create one.

---

## Step 2: Node.js Dependencies

Install Node.js packages for the backend:

```bash
npm install
```

This installs: express, mongoose, cors, bcryptjs, jsonwebtoken, dotenv, etc.

---

## Step 3: Environment Setup

### For Node.js Backend (`server.js`):
Create a `.env` file in the root directory:

```env
MONGODB_URI=mongodb://localhost:27017/healthify
JWT_SECRET=your-secret-key-here
NODE_ENV=development
PORT=3001
```

### For Python Services:
- **Chatbot (`chat/chatbot.py`)**: Set OpenAI API key:
  ```python
  openai.api_key = "your-openai-api-key"
  ```
  
- **Medical Summarizer (`medical_summary/report.py`)**: Already has Gemini API key (line 11), but you may want to secure it.
  
- **Food Detection App (`web_app/web_app/app.py`)**: Update model path (line 30):
  ```python
  model_path = "web_app/web_app/model/best.pt"  # Update this path
  ```
  Currently hardcoded to `D:/success/model/best.pt` - needs to be changed!

---

## Step 4: Start MongoDB

Make sure MongoDB is running:

```bash
# Windows (if installed as service)
net start MongoDB

# Or start manually
mongod
```

---

## Step 5: Run the Services

You need to run **multiple services** simultaneously. Open separate terminal windows:

### Terminal 1: Node.js Backend (Port 3001)
```bash
node server.js
```
This provides user authentication and calorie tracking APIs.

### Terminal 2: Food Detection App (Port 5001)
```bash
# Activate virtual environment first
env\Scripts\activate

# Navigate to the web_app directory
cd web_app\web_app

# Run the app
python app.py
```
Or from root:
```bash
env\Scripts\activate
python -m web_app.web_app.app
```

### Terminal 3: Medical Report Summarizer (Port 5003) - Optional
```bash
env\Scripts\activate
python medical_summary\report.py
```

### Terminal 4: Chatbot Service (Port 5002) - Optional
```bash
env\Scripts\activate
python chat\chatbot.py
```

---

## Step 6: Access the Application

- **Main Food Detection App**: http://localhost:5001
- **Frontend (Template folder)**: Open `template/index.html` in a browser (or serve via a local server)
- **Backend API**: http://localhost:3001
- **Medical Summarizer**: http://localhost:5003
- **Chatbot**: http://localhost:5002

---

## Important Notes

1. **Model Path Issue**: The `web_app/web_app/app.py` file has a hardcoded model path:
   - Current: `D:/success/model/best.pt`
   - Should be: `web_app/web_app/model/best.pt` (relative path)
   - Or: `web_app/model/best.pt` (if using the other model file)

2. **Model Files Available**:
   - `web_app/web_app/model/best.pt`
   - `web_app/model/best.pt`
   - `web_app/yolov8n.pt` (YOLOv8 nano model)

3. **Frontend Configuration**: The frontend (`template/script.js`) needs to be configured to connect to:
   - Backend API: `http://localhost:3001`
   - Food Detection API: `http://localhost:5001`

4. **API Keys**: Make sure to set your API keys:
   - OpenAI API key (for chatbot)
   - Gemini API key (already in medical_summary/report.py)

---

## Quick Start (Minimal Setup)

If you just want to test the main food detection:

```bash
# 1. Activate venv
env\Scripts\activate

# 2. Install flask and ultralytics
pip install flask flask-cors opencv-python ultralytics torch torchvision

# 3. Fix model path in web_app/web_app/app.py (line 30)
# Change: model_path = "D:/success/model/best.pt"
# To: model_path = "web_app/web_app/model/best.pt"

# 4. Run the food detection app
cd web_app\web_app
python app.py
```

Then open: http://localhost:5001

---

## Troubleshooting

1. **Model not found**: Update the model path in `web_app/web_app/app.py` line 30
2. **MongoDB connection error**: Make sure MongoDB is running
3. **Tesseract not found**: Install Tesseract OCR and update path in `medical_summary/report.py`
4. **Port already in use**: Stop other services using ports 3001, 5001, 5002, 5003


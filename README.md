# churn-prediction-api
# 🚀 Customer Churn Prediction — REST API

> Trained Random Forest churn model served as a production-ready REST API using FastAPI, deployed live on Render with auto-generated interactive documentation.

![FastAPI](https://img.shields.io/badge/FastAPI-latest-green)
![Python](https://img.shields.io/badge/Python-3.12-blue)
![scikit--learn](https://img.shields.io/badge/scikit--learn-ML-orange)
![Render](https://img.shields.io/badge/Deployed-Render-purple)
![Status](https://img.shields.io/badge/Status-Live-brightgreen)

---

## 🔗 Live API

| | Link |
|---|---|
| **Base URL** | https://churn-prediction-api-8hof.onrender.com |
| **Interactive Docs (Swagger UI)** | https://churn-prediction-api-8hof.onrender.com/docs |
| **Health Check** | https://churn-prediction-api-8hof.onrender.com/ |

> ⚠️ Hosted on Render free tier — first request after inactivity may take 30-60 seconds to wake up (cold start). Subsequent requests are instant.

---

## 📌 What This Does

Takes a telecom customer's account data as input and returns:
- **Churn probability** (0.0 to 1.0)
- **Churn prediction** (0 = stays, 1 = churns)
- **Risk level** (Low / Medium / High)
- **Business recommendation** (plain English action)

Built on top of the [Customer Churn Prediction project](https://github.com/YOUR_USERNAME/customer-churn-prediction) — a Random Forest model tuned with RandomizedSearchCV achieving AUC-ROC of 0.8243 and 77.5% churn recall.

---

## 🗂️ Project Structure

```
churn-prediction-api/
│
├── main.py                  ← FastAPI app with /predict and /predict_batch endpoints
├── churn_model.pkl          ← Serialized trained Random Forest (joblib)
├── feature_columns.pkl      ← Exact feature column order from training pipeline
├── requirements.txt         ← Python dependencies
└── README.md                ← You are here
```

---

## 📡 API Endpoints

### GET /
Health check — confirms API is running.

**Response:**
```json
{
  "message": "Churn Prediction API is running",
  "model": "Random Forest (AUC-ROC: 0.8243)",
  "endpoint": "/predict"
}
```

---

### POST /predict
Predicts churn for a single customer.

**Request body:**
```json
{
  "tenure": 2,
  "MonthlyCharges": 85.5,
  "TotalCharges": 171.0,
  "Contract_One_year": 0,
  "Contract_Two_year": 0,
  "InternetService_Fiber_optic": 1,
  "InternetService_No": 0,
  "PaymentMethod_Credit_card_automatic": 0,
  "PaymentMethod_Electronic_check": 1,
  "PaymentMethod_Mailed_check": 0
}
```

**Response:**
```json
{
  "churn_probability": 0.4805,
  "churn_prediction": 1,
  "risk_level": "Medium",
  "threshold_used": 0.35,
  "message": "Customer likely to churn — trigger retention campaign"
}
```

---

### POST /predict_batch
Predicts churn for multiple customers in one call.

**Response:**
```json
{
  "predictions": [
    {
      "churn_probability": 0.4805,
      "churn_prediction": 1,
      "risk_level": "Medium"
    }
  ],
  "total_customers": 1
}
```

---

## 💡 Key Design Decisions

**Why joblib for serialization?**
joblib is the recommended serialization method for scikit-learn models, it handles large numpy arrays (like Random Forest's 100+ trees) more efficiently than Python's built-in pickle.

**Why threshold 0.35 instead of default 0.5?**
Lowering the threshold from 0.5 to 0.35 increases churn recall from 60.4% to 77.5%, catching 64 additional churners per 1,409 customers. The business cost of missing a churner (losing them permanently) outweighs the cost of a false alert (unnecessary retention offer).

**Why feature column ordering matters?**
Random Forest predictions are sensitive to column order,sending features in the wrong order produces incorrect results. `feature_columns.pkl` stores the exact column sequence from the training pipeline, which `main.py` enforces on every prediction request.

**Why FastAPI over Flask?**
FastAPI auto-generates interactive Swagger documentation at `/docs`, has built-in Pydantic input validation, and is significantly faster than Flask due to async support, making it the modern standard for ML model serving.

---

## 🛠️ Tech Stack

| Category | Tool |
|---|---|
| Web framework | FastAPI |
| Server | uvicorn |
| ML model | scikit-learn Random Forest |
| Serialization | joblib |
| Input validation | Pydantic |
| Deployment | Render (free tier) |
| Language | Python 3.12 |

---

## ▶️ Run Locally

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/churn-prediction-api.git
cd churn-prediction-api

# Install dependencies
pip install -r requirements.txt

# Start the server
uvicorn main:app --reload
```

Then open:
- `http://127.0.0.1:8000` — health check
- `http://127.0.0.1:8000/docs` — interactive API documentation

---

## 🔗 Related Projects

| Project | Description |
|---|---|
| [Customer Churn Prediction](https://github.com/YOUR_USERNAME/customer-churn-prediction) | Source ML model — EDA, SMOTE, Random Forest, SHAP, threshold tuning |
| [Sentiment Analysis](https://github.com/YOUR_USERNAME/sentiment-analysis-nlp) | NLP pipeline — TF-IDF, Logistic Regression, DistilBERT comparison |

---

## 📝 Learnings

- **RTF vs plain text** — `requirements.txt` must be saved as plain text. Creating it in Mac's TextEdit (default RTF mode) caused `Invalid requirement` errors on Render. Fix: always use a code editor (VS Code) or terminal `cat` command for config files.
- **Version pinning on deployment** — pinning exact library versions in `requirements.txt` caused metadata generation failures on Render's build environment. Removing version pins and letting the package manager resolve compatible versions fixed the issue.
- **Render free tier cold starts** — services spin down after 15 minutes of inactivity. First request takes 30-60 seconds to wake up. Expected behaviour on free hosting — production deployments would use paid tier or a keep-alive ping service.
- **Feature column ordering** — discovered that sending features without enforcing training column order produces silent wrong predictions, not errors. Storing and reapplying `feature_columns.pkl` on every request prevents this.

---

## 🔗 Connect


**[Deepika J]**  
[LinkedIn](https://www.linkedin.com/in/deepika-jalamanchili/) · [GitHub](https://github.com/Deepika-J2106) · [deepikajalamanchili.9999@gmail.com](mailto:deepikajalamanchili.9999@gmail.com)

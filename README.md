Smart Order Fulfillment System


Explore

A comprehensive B2B logistics backend system designed to automate and optimize the assignment of customer orders to the most suitable warehouses. The system leverages geographic data, stock availability, and urgency with machine learning capabilities to ensure efficient order fulfillment and resource management.

✨ Key Features

CategoryFeatureDescriptionOptimizationAlgorithm-Driven DispatchSorts orders by urgency, calculates geodesic distances, and assigns orders to the nearest warehouse with sufficient stock.IntelligenceK-NN Machine LearningA K-Nearest Neighbors (k=3) model trained on successful assignments to provide real-time suggestions for new order fulfillment.InterfaceResponsive Web DashboardA Vanilla JS frontend with Chart.js visualization for real-time stock levels and assignment results.IntegrationsData API (Flask)A robust Flask backend for data upload, dispatch control, assignment retrieval, and ML predictions.PersistenceModel & Data ManagementUses Pandas for data handling and Joblib for efficient serialization and loading of the trained ML model.🏗️ Technical Architecture

The system follows a classic client-server architecture, using a lightweight Flask API for the backend logic and a static HTML/JS frontend for user interaction.

Backend Stack

Web Framework: Flask 3.0

Data Processing: Pandas 2.0

Machine Learning: Scikit-learn 1.3

Geospatial: Geopy 2.4 (for geodesic distance calculations)

Frontend Stack

Markup & Styling: HTML5 / CSS3 Grid (Mobile-first, responsive)

Interactivity: Vanilla JavaScript (No framework dependencies)

Data Visualization: Chart.js

Data Flow Overview

File Upload ($\text{CSV}$) $\rightarrow$ Backend validation $\rightarrow$ Storage in /data.

Dispatch Algorithm $\rightarrow$ Assignment logic $\rightarrow$ Training of $\text{KNN}$ model $\rightarrow$ Save results ($\text{CSV}$).

ML Prediction $\rightarrow$ Load model $\rightarrow$ Input $\text{Features}$ ($\text{Lat, Lon, Quantity, Urgency}$) $\rightarrow$ Return $\text{Suggested Warehouse ID}$.





Shutterstock

Explore

🚀 Quick Start Guide

Prerequisites

Python 3.8+

A modern web browser

1. Installation

Bash



# Clone the repository

git clone <repository-url> smart-order-fulfillment-webcd smart-order-fulfillment-web/backend# Create and activate virtual environment

python -m venv venvsource venv/bin/activate  # On Windows: venv\Scripts\activate# Install dependencies

pip install -r requirements.txt

2. Prepare Data (Optional)

To use the sample data provided:

Bash



# Create necessary directories

mkdir -p data models# Copy sample data

cp ../sample_orders.csv data/orders.csv

cp ../sample_warehouses.csv data/warehouses.csv

3. Run Server & Access Frontend

Bash



# From inside the 'backend' directory

python app.py

The API is now running at http://localhost:5000.

Open the web dashboard: Smart Order Fulfillment System Dashboard

⚙️ Core Logic: Order Dispatch Algorithm

The dispatch process prioritizes both urgency and proximity while ensuring stock availability.

Python



def dispatch_orders():

    # 1. Sort orders by urgency (High=0, Medium=1, Low=2)

    orders = orders.sort_by('urgency_rank')

     

    for order in orders:

        # 2. Find eligible warehouses with sufficient stock

        eligible = warehouses[warehouses.stock >= order.quantity]

         

        # 3. Calculate geodesic distances (using Geopy)

        distances = [geodesic(order.location, wh.location) for wh in eligible]

         

        # 4. ASSIGNMENT: Select the nearest eligible warehouse

        assigned_warehouse = eligible[min_distance_index]

         

        # 5. Update stock and record successful assignment

        assigned_warehouse.stock -= order.quantity

        record_assignment(order, assigned_warehouse)

    

    # After all assignments, the KNN model is TRAINED on the final results.

💻 Backend API Endpoints

MethodEndpointDescriptionRequest BodyPOST/uploadUpload orders.csv and warehouses.csv to system.multipart/form-dataPOST/dispatchExecute order assignment algorithm and train the ML model.NoneGET/assignmentsRetrieve the latest order assignment results.NonePOST/predictGet ML warehouse suggestion for a new order.JSON: {latitude, longitude, quantity, urgency}GET/download-assignmentsDownload the assignment results as a CSV file.NoneGET/warehouse-stockGet current stock levels for visualization.None🧩 Machine Learning Model

The system uses a simple but effective $\text{KNN}$ classifier to learn optimal assignments.

Algorithm: K-Nearest Neighbors ($\mathbf{k=3}$)

Features ($\mathbf{X}$): [latitude, longitude, quantity, urgency_rank]

Target ($\mathbf{y}$): warehouse_id

Training: Automatically occurs after every successful dispatch simulation.

Persistence: Trained model is saved using Joblib.

Sample Prediction Request

HTTP



POST /predict

Content-Type: application/json



{

  "latitude": 13.08,

  "longitude": 80.27,

  "quantity": 2,

  "urgency": "High"

}

🛡️ Security & Configuration

Security Features

File Validation: Strict $\text{CSV}$ format and size limits.

CORS: Configured via Flask-CORS for secure cross-origin requests.

Input Sanitization: Basic validation on all API inputs.

Key Environment Variables & Parameters

The application is configured using variables for flexibility:

SettingDefault ValueDescriptionKNN_NEIGHBORS3Number of neighbors for the $\text{KNN}$ model.UPLOAD_FOLDER./dataDirectory for storing uploaded CSV files.MAX_CONTENT_LENGTH16 MBMaximum size for file uploads.🤝 Contributing

We welcome contributions! Please follow these steps:

Fork the repository.

Create a new feature branch (git checkout -b feature/your-feature).

Commit your changes (git commit -m 'feat: Add new feature X').

Push to the branch (git push origin feature/your-feature).

Open a Pull Request with a clear description of your changes.

📝 License

This project is licensed under the MIT License. See the LICENSE file for details.

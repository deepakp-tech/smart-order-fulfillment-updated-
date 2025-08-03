# Smart Order Fulfillment System

A comprehensive B2B logistics backend system that automates the assignment of customer orders to optimal warehouses based on stock availability, geographic proximity, and urgency. The system includes machine learning capabilities to suggest warehouses and provides a responsive web dashboard for internal operations teams.

## 🌟 Features

### Backend API (Flask)
- **POST /upload** – Upload orders.csv and warehouses.csv files
- **POST /dispatch** – Run dispatch simulation with ML model training
- **GET /assignments** – Retrieve latest assignment results
- **POST /predict** – Get warehouse suggestions using trained ML model
- **GET /download-assignments** – Download assignments CSV file
- **GET /warehouse-stock** – Get current warehouse stock levels

### Frontend Dashboard
- **File Upload Interface** – Upload CSV files with validation
- **Dispatch Control** – Run order assignment algorithm
- **Assignment Results Table** – View order assignments with status highlighting
- **ML Prediction Form** – Get warehouse suggestions for new orders
- **Stock Visualization** – Real-time bar chart of warehouse inventory
- **Download Center** – Export assignment results

### Machine Learning
- **KNN Classifier** – Learns from assignment patterns (k=3)
- **Features**: Latitude, longitude, quantity, urgency ranking
- **Model Persistence** – Saves trained models using joblib
- **Prediction API** – Real-time warehouse suggestions

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- Modern web browser

### Installation

1. **Clone or download the project files**
   ```bash
   # All necessary files are provided
   ```

2. **Set up the backend**
   ```bash
   # Create project structure
   mkdir -p smart-order-fulfillment-web/backend/{data,models}
   cd smart-order-fulfillment-web/backend
   
   # Create virtual environment
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   
   # Install dependencies
   pip install -r requirements.txt
   ```

3. **Add sample data**
   ```bash
   # Copy sample CSV files to backend/data/
   cp sample_orders.csv backend/data/orders.csv
   cp sample_warehouses.csv backend/data/warehouses.csv
   ```

4. **Run the backend server**
   ```bash
   cd backend
   python app.py
   ```
   The API will be available at `http://localhost:5000`

5. **Access the frontend**
   
   Open the deployed web application:
   **[Smart Order Fulfillment System Dashboard](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/17e718094d150bc94958343a56ea411e/ab3f9a08-df88-4ba8-8992-cba6b7b33fed/index.html)**

## 📋 Usage Guide

### 1. Upload Data
- Use the **Data Upload** section to upload your CSV files
- **Orders CSV** format:
  ```csv
  order_id,latitude,longitude,quantity,urgency
  101,13.08,80.27,2,High
  102,26.85,80.94,1,Low
  ```
- **Warehouses CSV** format:
  ```csv
  warehouse_id,latitude,longitude,stock
  1,12.97,77.59,500
  2,28.61,77.20,600
  ```

### 2. Run Dispatch
- Click **"Run Dispatch"** to execute the order assignment algorithm
- The system will:
  - Sort orders by urgency (High → Medium → Low)
  - Find eligible warehouses for each order
  - Calculate geodesic distances
  - Assign orders to nearest available warehouses
  - Train a KNN model on successful assignments
  - Update warehouse stock levels

### 3. View Results
- **Assignment Table**: Shows order_id, assigned warehouse, distance, and status
- **Stock Chart**: Real-time visualization of remaining warehouse inventory
- **Download**: Export assignment results as CSV

### 4. ML Predictions
- Use the **Warehouse Suggestion** form to get ML-powered recommendations
- Input: latitude, longitude, quantity, urgency level
- Output: Suggested warehouse ID with confidence score

## 🏗️ Technical Architecture

### Backend Stack
- **Flask 3.0** – Web framework
- **Pandas 2.0** – Data processing
- **Scikit-learn 1.3** – Machine learning
- **Geopy 2.4** – Geographic distance calculations
- **Joblib 1.3** – Model persistence
- **Flask-CORS 4.0** – Cross-origin support

### Frontend Stack
- **HTML5** – Semantic markup
- **CSS3 Grid** – Responsive layout system
- **Vanilla JavaScript** – No framework dependencies
- **Chart.js** – Data visualization
- **Fetch API** – Asynchronous requests

### Data Flow
1. **File Upload** → CSV validation → Store in `/data`
2. **Dispatch Algorithm** → Process orders → Train ML model → Save results
3. **ML Prediction** → Load model → Predict warehouse → Return suggestion
4. **Visualization** → Fetch data → Update charts → Display results

## 🎯 Algorithm Details

### Order Assignment Logic
```python
def dispatch_orders():
    # 1. Sort orders by urgency (High=0, Medium=1, Low=2)
    orders = orders.sort_by('urgency_rank')
    
    for order in orders:
        # 2. Find warehouses with sufficient stock
        eligible = warehouses[warehouses.stock >= order.quantity]
        
        # 3. Calculate geodesic distances
        distances = [geodesic(order.location, wh.location) for wh in eligible]
        
        # 4. Assign to nearest warehouse
        assigned_warehouse = eligible[min_distance_index]
        
        # 5. Update stock and record assignment
        assigned_warehouse.stock -= order.quantity
        record_assignment(order, assigned_warehouse)
```

### Machine Learning Model
- **Algorithm**: K-Nearest Neighbors (k=3)
- **Features**: `[latitude, longitude, quantity, urgency_rank]`
- **Target**: `warehouse_id`
- **Training**: Automatic during dispatch process
- **Prediction**: Real-time suggestions for new orders

## 📊 API Documentation

### Upload Files
```http
POST /upload
Content-Type: multipart/form-data

Form fields:
- orders: CSV file
- warehouses: CSV file

Response: 200 OK
{
  "message": "Files uploaded successfully",
  "orders_count": 15,
  "warehouses_count": 6
}
```

### Run Dispatch
```http
POST /dispatch

Response: 200 OK
{
  "message": "Dispatch completed successfully",
  "assignments": [...],
  "summary": {
    "total_orders": 15,
    "assigned_orders": 13,
    "unassigned_orders": 2,
    "model_trained": true
  }
}
```

### Get Predictions
```http
POST /predict
Content-Type: application/json

{
  "latitude": 13.08,
  "longitude": 80.27,
  "quantity": 2,
  "urgency": "High"
}

Response: 200 OK
{
  "predicted_warehouse": 1,
  "confidence": 0.85,
  "input_data": {...}
}
```

## 🎨 UI/UX Features

### Responsive Design
- **Mobile-first** CSS Grid layout
- **Breakpoints**: 768px (tablet), 1024px (desktop)
- **Adaptive components** that scale gracefully

### Interactive Elements
- **Loading indicators** for all async operations
- **Status messages** with color-coded feedback
- **Real-time updates** after dispatch operations
- **Error handling** with user-friendly messages

### Accessibility
- **Semantic HTML** for screen readers
- **ARIA labels** on interactive elements
- **Keyboard navigation** support
- **High contrast** color scheme

## 🔧 Configuration

### Environment Variables
```bash
export FLASK_ENV=development
export FLASK_DEBUG=1
export UPLOAD_FOLDER=./data
export MAX_CONTENT_LENGTH=16777216  # 16MB
```

### Model Parameters
```python
KNN_NEIGHBORS = 3  # Number of neighbors for KNN
DISTANCE_THRESHOLD = 100  # Max distance in km
STOCK_BUFFER = 0  # Minimum stock buffer
```

## 📈 Performance Optimization

### Backend
- **Vectorized operations** using pandas
- **Efficient distance calculations** with geopy
- **Model caching** with joblib
- **Memory management** for large datasets

### Frontend
- **Debounced API calls** to prevent spam
- **Lazy loading** of chart components
- **Efficient DOM updates** with minimal redraws
- **Compressed assets** for faster loading

## 🛡️ Security Features

- **File validation** (CSV only, size limits)
- **CORS configuration** for cross-origin requests
- **Input sanitization** for all API endpoints
- **Error handling** without sensitive data exposure

## 🧪 Testing

### Manual Testing Scenarios
1. **Upload valid CSV files** → Should succeed with confirmation
2. **Upload invalid files** → Should show appropriate error messages
3. **Run dispatch with data** → Should assign orders and train model
4. **Get ML predictions** → Should return warehouse suggestions
5. **Download assignments** → Should export CSV file

### Sample Test Data
The project includes realistic sample data:
- **15 orders** across different Indian cities
- **6 warehouses** strategically located
- **Mixed urgency levels** (High, Medium, Low)
- **Varying quantities** (1-5 units)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🆘 Troubleshooting

### Common Issues

**Backend won't start**
- Check Python version (3.8+ required)
- Verify all dependencies are installed
- Ensure ports 5000 is available

**File upload fails**
- Check file format (CSV only)
- Verify file size (< 16MB)
- Ensure correct column headers

**ML predictions not working**
- Run dispatch first to train the model
- Check if model file exists in `/models`
- Verify input data format

**Charts not displaying**
- Check browser console for JavaScript errors
- Ensure Chart.js library is loaded
- Verify API responses contain valid data

### Support
For technical support or questions, please open an issue in the project repository.

---

**Built with ❤️ for efficient B2B logistics operations**#   s m a r t - o r d e r - f u l f i l l m e n t - u p d a t e d -  
 #   s m a r t - o r d e r - f u l f i l l m e n t - u p d a t e d -  
 
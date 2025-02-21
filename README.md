# Efficient Supply Allocation - Driver to Zone Allocation

## Overview
This project focuses on predicting demand at specific times and locations to direct drivers toward areas with higher expected demand, ensuring a better match between riders and drivers.
The project uses - Auto ARIMA, Random Forest regressor, LSTM

## Dataset
The dataset consists of ride transactions for a Month for a city . It includes start and end latitude/longitude along with ride value.
Sample Data 

| start_time | start_lat | start_lng | end_lat | end_lng | ride_value | 
|--------|------------------------|------------------------|------------------------| ------------------------|------------------------|
| 2025-09-01 | 59.44435485 | 24.83000276 | 59.36179989 | 24.74020603 | 0.6005 | 

## Approach
1. **Exploratory Data Analysis (EDA)**
   - Basic data sanity checks: missing values, outliers.
   - Temporal analysis: hourly, daily, weekly demand trends.
   - Spatial analysis: demand shifts through the day, zone creation using geohashing and K-means clustering.
   - Lag-based analysis: correlation between past and current ride values.
   - Insights to identify features for modeling.

2. **Feature Engineering**
   - **Spatial Features**: Zone IDs, distance to city center, proximity to popular zones.
   - **Lag Features**: Historical demand patterns (e.g., last 30 minutes, 1 hour, 1 day).
   - **Ride Value**: Aggregated metrics like average ride value per zone and time.

3. **Modeling and Comparison**
   - **Auto ARIMA**: Univariate time series model.
   - **Random Forest Regressor**: Includes lag, spatial, and ride value features.
   - **LSTM-based Forecasting**: Using the same feature set.
   
4. **Evaluation Metrics**
   - Mean Absolute Error (MAE) for model comparison.
   - RF regressor and LSTM performed significantly better than ARIMA.

#### Results Comparison (Mean Squared Error)
| Model | Ride Value Prediction | Ride Count Prediction |
|--------|------------------------|------------------------|
| Auto ARIMA | 33,176.31 | 221.14 |
| Random Forest | 1,750.44 | 50.96 |
| LSTM | 7,008.28 | 48.39 |

## Production Workflow / Driver Simulation
1. Simulate 20 drivers across the city.
2. Assign each driver to a zone.
3. Get demand forecasts from the best-performing model (RF Regressor).
4. Compare demand and earnings between zones and provide recommendations.
5. Output recommendation as a JSON response for UI consumption.

### Example JSON Output
```json
{
  "recommendation": "Move to another zone",
  "current_zone": 16,
  "current_zone_earnings": 34.46,
  "current_zone_drivers": 4,
  "top_zones": [
    {"zone": 8, "earnings": 536.10, "extra_earnings": 501.64, "drivers_in_zone": 1},
    {"zone": 1, "earnings": 495.22, "extra_earnings": 460.76, "drivers_in_zone": 2},
    {"zone": 2, "earnings": 484.06, "extra_earnings": 449.60, "drivers_in_zone": 4}
  ]
}
```

## Next Steps
- **Model Experiments**:
  - Reinforcement Learning for driver decision optimization.
  - CNN for modeling spatial patterns.
  - Experimenting with Prophet/DeepAR for seasonality handling.
- **Data Augmentation**:
  - Expanding dataset for a longer time period.
  - Incorporating external factors (weather, events, traffic) for better predictions.
- **Validation & Metrics**:
  - Visualizing demand hotspots.
  - Developing a dashboard for real-time demand insights.
  - Using additional metrics: driver idle time, wait time, and earnings.

## Deployment Strategy
1. **Containerization with Docker**
   - Package the model into a Docker image.
   - Deploy via a container registry.

2. **Kubernetes for Scaling**
   - Automate scaling with Kubernetes pods.
   - Use Horizontal Pod Autoscaler for demand-based scaling.

3. **Real-Time Data Ingestion with Kafka**
   - Handle incoming ride data in real-time.
   - Organize data streams for ride requests, driver locations, and traffic.

4. **REST API for Driver Mobile App**
   - API endpoints to provide zone recommendations.
   - Developed using Flask or FastAPI.

5. **Kubeflow for ML Pipeline**
   - Automate model training and hyperparameter tuning.
   - Deploy updated models automatically.

6. **DBT for Data Transformation**
   - Preprocess ride data.
   - Generate features for machine learning models.

## Experiment Design for Live Operations
- **Hypotheses**:
  - New allocation recommendations improve driver earnings and reduce idle time.
- **Randomized Driver Assignment**:
  - Control group follows current strategy.
  - Test group gets real-time recommendations.
- **Metrics Measured**:
  - Driver earnings, idle time, rider wait time.
- **Analysis**:
  - Use statistical tests to validate improvements.
  - Segment analysis to identify performance variations.

## Architecture Components
- **Data Ingestion Layer**: Collects live data (e.g., ride requests, driver locations).
- **Model Serving Layer**: Hosts demand forecasting models.
- **Recommendation Engine**: Converts predictions into actionable insights.
- **Monitoring & Retraining**: Tracks model performance and automates updates.

## Summary
This project demonstrates an end-to-end solution for optimizing driver allocation using demand forecasting models. The approach integrates machine learning, real-time data ingestion, and scalable deployment strategies to improve operational efficiency in ride-hailing platforms.

# ride-hailing-demand-supply
Predict demand at specific times and places. It will enable us to direct drivers toward areas with higher expected demand, ensuring a better match between riders and drivers. 


Efficient Supply allocation - Driver to zone allocation 
Akshay Paliwal

Overview & Status 

Category
Task
Status
Notebook
 EDA
Basic data sanity checks - missing values, outliers etc
 Completed
1
 EDA
Time based analysis 
Hourly, daily, weekly 
Lag related features 
 Completed
1
 EDA
Space based analysis
How demands shifts through the day
Zone creation only using lat, long
Kmeans + geohashing
Zone creation using revenue + lat, long
Kmeans + geohashing
 Completed
1
 EDA
Combination of Space + Time
Correlation between space, time 
 Completed
1
 EDA
Conclusion and take away 
Identify the features for the model creation 
 Completed
1
 Feature creation
Zone creation
Temporal features 
Day, hour, week, is_holiday
Lags related features 
Space related features
Distance from important locations 
Aggregated features per zone
Average ride value 
Total rides
Total ride value 
 Completed
2
 Model creation and comparision
Arima model and visualization
 Completed
2
 Model creation and comparision
RF regressor and visualization
 Completed
2
 Model creation and comparision
LSTM and visualization
 Completed
2
 Model creation and comparision
Comparison between the three models for demand forecasting using MSE ( mean squared error) 
 Completed
2
 Production usage & simulation test
Simulating real world example
Create 20 drivers around the city 
Get their zones 
Get their zone’s demand
Give recommendations 
 Completed
3
 Streach goals
Mode, Data and validation next steps 
 Not Started
-




Overview & Status	1
Objective	3
Approach	3
EDA	3
Feature engineering	4
Modeling Creation and comparison	4
Metrics - outcome	5
Production workflow / Driver usage / Simulation	6
Next steps	7
With respect to model experiments :	7
Data augmentation :	7
Validation and new metrics :	7
Deployment Strategy	7
Architecture Components	10
Experiment Design for Live Operations	10
Define Hypotheses	10
Experiment Design	11
1. Randomized Driver Assignment	11
2. Duration	11
3. Metrics to Measure	11
4. Marketplace-Specific Considerations:	11
5. Analysis and Reporting	11

Objective 
We need to predict demand at specific times and places. It will enable us to direct drivers toward areas with higher expected demand, ensuring a better match between riders and drivers. 

The data is for one month - March 2022 for the city of Tallin, Estonia with ride transactions from people. It consists of start and end latitude, longitude along with ride value. 
Approach 
Our approach is first to understand data through EDA, takeaway actionable insights which should help us in creation of our feature set. While doing the EDA we are not making the code as functions/reusable components because the function of EDA is to understand the data. 

But once we are done with EDA and move on to feature generation, model creation, simulation and recommendation - almost all the things are created as functions so that we don’t end up spending time when we want to put this to use. This should be a general practice which helps in saving a lot of time. 

EDA
All this will be found inside the notebook EDA - “NB1 - EDA”. Just listing all this here for an overview of the whole exercise. Please have a look at the notebook for detailed analysis. 
BASIC EDA - So that we know what kind of detailed EDA we should do
Missing value check
Data description 
Outliers


Temporal analysis
Hourly, daily, weekly demand in terms of revenue 


Spatial analysis
Demand shifts throughout the day
Zone creation approaches
Geohashing 
K means 


Lag based analysis
Correlation between current ride value vs 1 hr , 1 day, 7 days etc 


Combination Time + Spacial
Correlation between spatial distribution of rides, ride value and demand peak 
High ride areas have consistently higher ride values during specific times? 


Takeaways for feature generation 

Feature engineering 
Spatial Features
 Zone IDs, distance to city center, proximity to popular zones.
Lag Features
 Historical demand patterns in the last `n` hours (e.g., demand in the last 30 minutes, 1 hour, etc.).
Ride Value
 Aggregated metrics like average ride value per zone and time.

Modeling Creation and comparison 
Approach 1 - Apply auto Arima model for each Zone using only univariate time series model. 
Approach 2 - Apply RF regressor with these features 

'lag_total_value_last_30_min', 'lag_total_value_last_1_hr', 'lag_total_value_last_1_day',
'lag_ride_count_last_30_min', 'lag_ride_count_last_1_hr', 'lag_ride_count_last_1_day',

 'Average_ride_value',


 'distance_to_hotspot_1_from_start', 'distance_to_hotspot_2_from_start', 'distance_to_hotspot_3_from_start', 'distance_to_hotspot_4_from_start', 'distance_to_hotspot_5_from_start', 'distance_to_hotspot_6_from_start', 'distance_to_hotspot_1_from_end', 'distance_to_hotspot_2_from_end', 'distance_to_hotspot_3_from_end', 'distance_to_hotspot_4_from_end', 'distance_to_hotspot_5_from_end', 'distance_to_hotspot_6_from_end'

Approach 3 - Apply LSTM based forecasting using the same set of features 

Metrics - outcome 
We are using Mean Absolute Error (MAE) for model comparison and evaluation. 
 
We did forecasting for both ride count and ride value - RF regressor and LSTM were doing much better than ARIMA model which does not use any features. Out of the three the RF regressor does better. 

RESULT COMPARISION RIDE COUNT
MEAN SQ ERROR AUTO ARIMA ONLY USING TIME PREDICTING RIDE VALUE: 221.1394363487784
MEAN SQ ERROR RF Regressor USING ALL FEATURES PREDICTING RIDE VALUE: 50.9557391119794
MEAN SQ ERROR LSTM USING ALL FEATURES PREDICTING RIDE VALUE: 48.391475597794226
#################################
RESULT COMPARISION RIDE VALUE
MEAN SQ ERROR AUTO ARIMA ONLY USING TIME PREDICTING RIDE VALUE: 33176.31143010126
MEAN SQ ERROR RF Regressor USING ALL FEATURES PREDICTING RIDE VALUE: 1750.4422926144439
MEAN SQ ERROR LSTM USING ALL FEATURES PREDICTING RIDE VALUE: 7008.28195477962




Production workflow / Driver usage / Simulation 
1. Simulate 20 drivers across Tallinn Estonia - get a random set of lat and long for each driver
2. Assign a zone in which they are 
3. Get the forecasted results from the best model for each zone - in our case it was RF regressor model
4. For each driver show the comparison of both demand and average money between zones
    a. Get the zone in which he is and also get all the candidate neighbor zones - keeping proximity in mind these zones should be neighbor zones 
    b. Get the forecasted number of rides there are in that zone vs next candidate zones
    c. Show him the gain or loss of moving from zone he is currently in to candidate zones 

The result json will look like this - which can be sent to the UI to consume  
{
'recommendation': 'Move to another zone',
 'current_zone': 16,
 'current_zone_earnings': 34.461919183828954,
 'current_zone_drivers': 4,
 'top_zones': [{'zone': 8.0, 'earnings': 536.0991615854606, 'extra_earnings': 501.6372424016316, 'drivers_in_zone': 1},
 {'zone': 1.0, 'earnings': 495.2200853390525, 'extra_earnings': 460.75816615522353, 'drivers_in_zone': 2},
 {'zone': 2.0, 'earnings': 484.06174518240005, 'extra_earnings': 449.5998259985711, 'drivers_in_zone': 4}]
}


Next steps 
These are the few next steps which I will like to try: 
With respect to model experiments : 
Reinforcement Learning Models: Optimize the driver’s decision to move between zones based on expected demand. The driver will act as an agent
We can even try out Convolutional Neural Networks (CNN): To model spatial patterns across zones (e.g., modeling the grid as an image).
Experiment with Prophet or DeepAR for seasonality 
When giving the prescription to the drivers - take into consideration the adjacency of the zones into account. If the candidate suggested zones are neighbors of the current driver zones. 
Data augmentation : 
Experiment with larger dataset - bigger time frame
Consider external data like weather, events, and traffic conditions in real time for enhanced demand predictions.
Validation and new metrics : 
Visualize demand hotspots and compare predictions with actual data
Develop a dashboard to visualize real-time demand, predictions, and suggested driver allocations.
Analyze model predictions and identify underperforming areas
For driver allocation models, use average driver idle time, average wait time for passengers and driver earnings as metrics.

Deployment Strategy
1. Containerization with Docker
Package the model into a portable, self-contained unit that can be deployed consistently across environments. Docker allows us to bundle the model, its dependencies (libraries, environment settings), and other requirements into an image. This ensures  that the model runs the same way everywhere, whether it’s in development, staging, or production.
We can follow these steps :
Write a `Dockerfile` to define the image for our model, including its dependencies (Python libraries, model weights, etc.).
Build the Docker image example: `docker build -t ride-allocation-model .`
Push the image to a container registry (e.g., Docker Hub, AWS ECR, Google Container Registry).

2.  Kubernetes for Orchestration and Scaling
Kubernetes lets us automatically scale our model up or down to meet demand spikes and manage resources efficiently.Kubernetes (K8s) is a container orchestration tool that automates the deployment, scaling, and management of containerized applications.

We can create Kubernetes pods that run instances of our Dockerized model. As demand grows (e.g., during rush hours), Kubernetes can automatically scale out (increase the number of pods), and during low demand, scale down to save resources.
Kubernetes Horizontal Pod Autoscaler (HPA) can automatically adjust the number of pods based on CPU/memory usage or custom metrics like the number of incoming ride requests.
Create Kubernetes Deployment and Service YAML files to define how our model should run in the cluster and expose it via a REST API.
Deploy our Dockerized model in the Kubernetes cluster.

3. Real-Time Data Ingestion with Apache Kafka
Kafka can handle incoming ride data in near real-time and ensure timely recommendations for ride allocation.It is a distributed streaming platform that can handle large volumes of real-time data.

It will be used to collect data such as ride requests, driver locations, and traffic conditions.
Kafka's topics will allow us to organize the data streams: example -
rides-topic for incoming ride requests.
drivers-topic for driver availability and locations.
traffic-topic for current traffic conditions.


   2. Set up Kafka producers to stream ride requests, driver locations, and other contextual data.
   3. Deploy Kafka consumers in the backend service to read from these topics and feed the data into the model.
   4. Use Kafka Streams for lightweight processing, such as filtering or enriching incoming data before it reaches the model.

4. REST API for Driver Mobile App
We will expose our model via a REST API, allowing the mobile app to get real-time recommendations.
The API will handle requests like:
     GET /recommendation: Given the driver’s current location and zone, return the best zone to move to for maximizing earnings.

We can develop the REST API in a web framework like Flask or FastAPI.
Containerize the REST API and deploy it in Kubernetes alongside the model.
The API will handle the driver’s input and query the model to provide recommendations.

   User interaction flow will be like:
   1. The driver’s app sends the current location and other context to the API.
   2. The API queries the model to generate recommendations.
   3. The API responds with the recommended zone and potential earnings.

5. Kubeflow for ML Model Pipeline and Management
We can automate and manage the model training and deployment lifecycle using Kubeflow. Kubeflow is a machine learning toolkit designed to run on Kubernetes. It helps us manage the end-to-end ML pipeline, from data ingestion, model training, hyperparameter tuning, to deployment.

We can use Kubeflow Pipelines to:
Automate the training of new models when fresh data comes in (e.g., new ride data).
Run hyperparameter tuning experiments to optimize your model.
Deploy the model directly to Kubernetes when a new, improved version is ready.

  

6. DBT for Data Transformation and Analytics
We can use DBT to Ensure smooth transformation and analysis of historical data. It also helps us organize our code into a more model centric code structure. 
We can use it for:
Cleaning and to preprocess large volumes of ride data (e.g., completed rides, driver availability history) to generate features for the ML model.
Build data pipelines that handle data ingestion, transformation, and loading into the machine learning pipeline.
   Example:
We can use DBT to preprocess and aggregate historical ride data and generate features (e.g., ride demand by zone, average earnings, traffic patterns) that can be used to retrain the model.

Example Workflow:

1. Driver request: A driver requests real-time recommendations from the mobile app.
2. Data streaming: Ride requests and driver locations are streamed into the system via Apache Kafka.
3. API Call: The mobile app calls the REST API to get recommendations for a particular driver.
4. Model query: The API queries the model to generate ride allocation recommendations.
5. Response: The API responds to the mobile app with the best zone for the driver, based on real-time data.
6. Model updates: As new data streams in, Kubeflow Pipelines automate model retraining. DBT transforms historical data, and the updated model is deployed via Kubernetes.

Benefits of This Strategy:
- Scalability: Kubernetes allows the system to scale up automatically during demand peaks.
- Real-Time Capabilities: Kafka enables the system to handle ride and driver data in near real-time.
- ML Lifecycle Automation: Kubeflow handles the entire ML pipeline, from training to deployment.
- Efficient Data Handling: DBT ensures smooth transformation and handling of historical data for analytics and model training.

Architecture Components
Data Ingestion Layer: Streams live data (e.g., ride requests, driver positions) to a central data store (e.g., AWS Redshift or GCP BigQuery).
Model Serving Layer: Hosts the demand forecasting and driver allocation models.
Recommendation Engine: Converts predictions into actionable driver recommendations.
Monitoring and Retraining Pipeline: Monitors model performance and triggers automated retraining if prediction error exceeds a certain threshold.


Experiment Design for Live Operations
Designing an experiment to validate the solution in a live environment involves a structured A/B testing framework. Here’s how I would set it up:
Define Hypotheses
H0 (Null Hypothesis): The new supply allocation recommendations do not improve driver earnings or reduce rider wait times compared to the current approach.
H1 (Alternative Hypothesis): The new recommendations lead to higher driver earnings, reduced idle time, and lower rider wait times.

Experiment Design
1. Randomized Driver Assignment
   Divide the drivers into two groups:
   Control Group: Drivers continue to follow the existing supply allocation strategy.
   Test Group: Drivers receive real-time recommendations from the new demand forecasting model.
2. Duration
Run the experiment for at least 2-4 weeks to capture variations in demand over different times of the day and week.
3. Metrics to Measure
  Driver Earnings: Compare average hourly earnings between the control and test groups.
  Driver Idle Time: Measure the average time drivers are idle between rides.
  Rider Wait Time: Track average wait times and cancellation rates to gauge customer satisfaction.
  Acceptance Rate: Measure how often drivers in the test group follow the recommendations.
4. Marketplace-Specific Considerations:
   Ensure there is no oversupply in high-demand zones, which can create congestion.
   Adjust dynamically based on driver feedback: Use driver feedback to refine the model or the way recommendations are presented.
5. Analysis and Reporting
Use t-tests or ANOVA to statistically validate the impact on earnings and wait times.
Perform segment analysis to identify if the solution works better for specific driver segments (e.g., new drivers vs. experienced drivers).




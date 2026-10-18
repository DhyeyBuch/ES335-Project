# **A running log for all the tasks done in the project so far...**

## **Dataset 1. The NYC Yellow Taxi Trip Dataset** (Jan 2016)

### **Target**
- Predict the duration of the taxi trip at pickup time

### **Early thoughts**
- Finalized dataset #1 for the project; it has good scope for showing the importance of feature engineering. Various useful features can be engineered from the given limited set of features.
- No random train/val/test split; we will have to split based on time to avoid leakage.
- Also, over time, the underlying distribution may change, which violates the assumption of supervised learning... A chronological split will expose our model to that more honestly.

### **Scope for Preprocessing and Feature Engineering**
- The feature `Extra` includes the $0.50 and $1 rush hour and overnight charges; we could derive a useful feature like rush hour from here.
- Could use the drop-off latitude and longitude to classify busy regions
- We could extract various useful features from Date and time - the hour of day could be segregated into 3-4 slots - peak hours, lazy hours, etc. 
- Make sure to remove the feature of `dropoff_datetime` since we are going to predict the taxi trip duration (make the difference of dropoff and pickup the target variable)
- Could potentially use log transformation for target variable due to right skewness (`np.log1p`)

### **Initial Cleaning and Transformation of Data**
- Some very noticeable outliers (such as a taxi trip spanning 83 days or negative value of duration), so will remove them
    - 11429 rows with total duration <= 0 (~0.1% of total data) which bought down the Standard Deviation of the data from 86 to 10 !!!
    - Next removed 8586 rows which had average speed > 100 miles per hour which is almost impossible in the NYC traffic. 
    - 391 rows removed as they had distance over 50 km and the ones with zero distance trips also removed

### **Fitting a baseline model on the raw data and the cleaned data**
- Linear Regression on 500k sampled rows
- **IMP**: Not using the columns: `total_duration`, `dropoff`, `trip_distance`, `avg_speed_mph` as they are not available during prediction time and would cause data leakage. 

- **Linear Regression**: 

    - **On the Raw data**
        - RMSE: 57.21 minutes
        - MAE: 10.21 minutes

    - **On the clean data**
        - RMSE: 52.78 minutes
        - MAE: 9.86 minutes

- **Decision Tree Regressor (only on the cleaned data)**

    - **On the Train data**
        - RMSE: 4.8801
        - MAE: 0.1155
    
    - **On the test data**
        - RMSE: 138.5187
        - MAE: 9.4284

### **Feature Engineering 1. Deriving Haversine distance from coordinates**
- Found very low correlation between distance and trip duration, which is suspicious and on investigation found several invalid coordinates. 
- Investigation of long durations also showed clearly that several records are plain wrong - duration nearly 24 hours for trips <10 miles. 

- **Final Cleaning Strategy**:
    - Remove non-positive durations.
    - Remove trips with invalid coordinates, using a NYC-area bounding box.
    - Remove zero or negative recorded distances.
    - Remove impossibly high average speeds (our existing 100 mph threshold).
    - Exclude the suspicious near-24-hour durations. Will keep an upper limit of 120 minutes for V1
    - Keep the 50-mile distance cutoff we've already investigated.



- AND VOILA!!! First great result to show for the tons of garbage cleaning I had to do
    - **Linear Regression**:
        - RMSE: 6.4735
        - MAE: 4.3766
    
    - **Decision Tree Regressor**:
        - RMSE: 6.2860
        - MAE: 4.1926

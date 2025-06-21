# 🚚 Delhivery Logistics Data Analysis Case Study

## 🏢 About Delhivery

**Delhivery** is India's largest and fastest-growing fully integrated logistics company. It provides end-to-end supply chain solutions with cutting-edge technology, operational excellence, and a large-scale network.

## 🎯 Objective

This case study focuses on understanding and processing logistics data to:

- Clean and engineer useful features from raw delivery data.
- Aggregate segment-level data into trip-level insights.
- Compare actual vs predicted (OSRM) metrics.
- Support forecasting and route optimization with data-driven analysis.

## 📁 Dataset Overview

### 📥 Dataset: [delhivery_data.csv](https://drive.google.com/file/d/1-SgMAgZOMS-OIwK6HRM4JMD-Okzk81C8/view?usp=drive_link)  

| Column Name                      | Description                                                   |
| -------------------------------- | ------------------------------------------------------------- |
| `data`                           | Indicates whether the row belongs to training or testing data |
| `trip_creation_time`             | Timestamp when the trip was created                           |
| `route_schedule_uuid`            | Unique identifier for a specific route schedule               |
| `route_type`                     | Type of transportation (`FTL` or `Carting`)                   |
| `FTL`                            | Full Truck Load - trips without intermediate stops            |
| `Carting`                        | Uses small vehicles making multiple stops                     |
| `trip_uuid`                      | Unique identifier for a trip (can have multiple segments)     |
| `source_center`                  | Source ID where the trip originated                           |
| `source_name`                    | Name of the source warehouse/location                         |
| `destination_cente`              | Destination center ID                                         |
| `destination_name`               | Name of the destination warehouse/location                    |
| `od_start_time`                  | Timestamp when the trip started                               |
| `od_end_time`                    | Timestamp when the trip ended                                 |
| `start_scan_to_end_scan`         | Duration between first and last scan of the delivery          |
| `is_cutoff`                      | Unknown field (possibly binary flag)                          |
| `cutoff_factor`                  | Unknown field (numeric/categorical)                           |
| `cutoff_timestamp`               | Unknown timestamp field                                       |
| `actual_distance_to_destination` | Actual distance between source and destination (in km)        |
| `actual_time`                    | Total actual time taken for the trip (in minutes)             |
| `osrm_time`                      | Predicted time from OSRM engine (cumulative, in minutes)      |
| `osrm_distance`                  | Predicted distance from OSRM engine (cumulative, in km)       |
| `factor`                         | Unknown field                                                 |
| `segment_actual_time`            | Time taken for this segment of the delivery (in minutes)      |
| `segment_osrm_time`              | OSRM predicted time for this segment (in minutes)             |
| `segment_osrm_distance`          | OSRM predicted distance for this segment (in km)              |
| `segment_factor`                 | Unknown field                                                 |


## 🎯 Exploratory Goals 

- **Data Cleaning**  
  - Handle missing values and drop irrelevant columns.
  - Format datetime fields and convert data types for memory efficiency.

- **Aggregation Strategy**  
  - Group by `trip_uuid` to merge segment rows into trip-level summaries using:
    - `sum()` for time/distance
    - `first()` or `last()` for non-aggregatable fields

- **Feature Engineering**  
  - Extract `month`, `year`, `hour` from `trip_creation_time`
  - Derive `trip_duration` from `od_end_time - od_start_time`
  - Parse `source_name` and `destination_name` into city and state

- **Hypothesis Testing**  
  - Compare **OSRM vs actual** metrics (time, distance)
  - Compare **segment-level** vs **trip-level metrics**
  - Use t-tests, Levene’s test, and visual analysis

- **Outlier Detection**  
  - Use **IQR** method to detect and clip **extreme values**

- **Encoding & Scaling**  
  - One-hot encode `route_type`
  - Normalize numerical features using MinMaxScaler


## 📊 Key Analysis & Results

- Data spans from **12th September 2018** to **8th October 2018**
- **14,817** unique trips, **938** unique source centers, **1,042** unique destination centers
- **690** source cities and **806** destination cities
- Majority of data used for **training**, not testing
- Most common route type: **Carting**
- **14 unique location IDs** missing in source/destination names
- Trip creation spikes after **noon**, peaking at **10 PM**
- Most trips occur in **week 38** of the year
- Highest trip volume during the **middle of the month**

### 🏙️ City & State Highlights

- **Top source states**: Maharashtra, Karnataka, Haryana, Tamil Nadu, Telangana
- **Top source cities**: Mumbai, Gurgaon, Delhi, Bengaluru, Bhiwandi
- **Top destination states**: Maharashtra, Karnataka, Haryana, Tamil Nadu, Uttar Pradesh
- **Top destination cities**: Mumbai, Bengaluru, Gurgaon, Delhi, Chennai

### 🧪 Statistical Test Summary

| Comparison                                      | Result                     |
|------------------------------------------------|----------------------------|
| Actual Time vs OSRM Time (Trip-level)          | ✅ Statistically different |
| Actual Time vs Segment Actual Time (Trip-level)| ❌ Statistically same      |
| OSRM Distance vs Segment OSRM Distance         | ✅ Statistically different |
| OSRM Time vs Segment OSRM Time                 | ✅ Statistically different |


## 💡 Business Insights

1. **Significant delay** observed in actual delivery times vs OSRM estimates.
2. **Mumbai dominates** as both source and destination — optimize routes here.
3. **Segment-wise metrics provide more realistic insight** than OSRM predictions alone.
4. Discrepancies in distance and time point to **routing inefficiencies**.
5. **Route type** (FTL vs Carting) can influence variance — ideal for modeling.
6. Delivery demand is heavily concentrated in **5 major states**—strong infrastructure focus needed here.


## ✅ Recommendations

1. **Enhance OSRM Accuracy**  
   Improve OSRM engine by integrating real-time traffic, seasonal trends, road conditions, and terrain data to reduce prediction discrepancies.

2. **Route Compliance Monitoring**  
   Track deviations between planned (OSRM) and actual routes using GPS data to minimize inefficiencies and delays.

3. **Optimize High-Traffic Corridors**  
   Prioritize infrastructure, staffing, and delivery resources in top-performing regions like **Mumbai, Bengaluru, Gurgaon, Maharashtra, and Karnataka**.

4. **Customer Profiling & Demand Forecasting**  
   Leverage regional behavioral patterns and engineered features (e.g., trip hour, route type) in machine learning models to better predict demand.

5. **Technology Integration**  
   Adopt advanced systems for **real-time route optimization**, **predictive ETAs**, and proactive delivery adjustments during high-demand periods.

6. **Outlier Detection & Process Monitoring**  
   Use IQR-based thresholds to identify and manage performance anomalies for continuous operational improvement.

7. **Continuous Improvement Culture**  
   Establish data-driven feedback loops from logistics data and customer input to refine processes and elevate service quality.


## 🛠️ Tools & Libraries

- **Python:** `Pandas`, `NumPy`, `Matplotlib`, `Seaborn`
- **Statistical Analysis:** `Scipy` (T-tests, Levene's), `statsmodels`
- **Feature Scaling:** `sklearn.preprocessing`, `MinMaxScaler`, `StandardScaler`
- **Jupyter Notebook** for analysis and documentation

## 📄 Output Report

📎 [delhivery_case_study_akansha_saini.pdf](https://github.com/AkanshaSaini761/Delhivery_Feature_Engineering_Case_Study/blob/main/delhivery_case_study_akansha_saini.pdf)  
Contains full data pipeline, visualizations, test summaries, and final insights.

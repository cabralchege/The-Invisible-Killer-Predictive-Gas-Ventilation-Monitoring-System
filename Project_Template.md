# The Invisible Killer: Predictive Gas & Ventilation Monitoring System
Predicting the invisible 10 minutes before it strikes.

## Problem Overview
In underground coal mines and Oil & Gas rigs, colorless, odorless gases (primarily Methane $CH_4$ and Carbon Monoxide $CO$) accumulate in pockets. These environments rely heavily on mechanical ventilation to keep air breathable.

## Problem Statement
Current safety systems are reactive. They trigger alarms only after a gas threshold is breached. If a ventilation fan fails or a pocket is hit, the time between the alarm ringing and a fatal explosion or asphyxiation is often too short for safe evacuation.

### Evidence / Statistics
1. Safety Stats: The ILO estimates that 8% of fatal occupational accidents occur in mining/underground work, largely due to gas explosions or asphyxiation.
2. Latency: Standard sensors often have a 30–90 second delay, which is too slow during a rapid gas outburst.

The Data Source: This project utilizes the UCI Machine Learning Repository: Methane Concentration in Underground Coal Mines dataset (derived from a real Polish coal mine).
Sensor Grid: The dataset includes readings from 28 sensors (Anemometers for wind speed, Methane sensors) recorded at high frequency.
Risk Factor: Methane explosions are the leading cause of mass fatalities in mining operations globally.
### Impact of the Problem
- Miners: Immediate threat to life via poisoning or combustion.
- Operations: "False alarms" or late warnings cause panic and costly operational shutdowns.

## Goals & Objectives
1. To develop a Time-Series Forecasting model that predicts a "Gas Spike" 10 minutes in advance by correlating airflow velocity drops with subsequent methane rises.
2. Sensor Fusion: Successfully combine Airflow Velocity (ventilation status) with Methane Level readings to learn physical causality.
3. Train the model to recognize the "Lag Effect" (e.g., Airflow drops at $T=0 \rightarrow$ Methane rises at $T+5$).
4. SCADA Visualization: Build a Streamlit dashboard that mimics a control room interface with real-time simulation capabilities.

## Impact Metrics (KPIs)
- Prediction Horizon: Validated warning signals at least 600 seconds (10 mins) before threshold breach.
- Model Accuracy: Low RMSE on the "dotted red line" prediction vs. actuals.
- Latency: Dashboard update speed $< 1$ second.

## Expected Outcomes
The Brain: An LSTM or Prophet-based ML model capable of multivariate forecasting.
The Interface: A "Digital Twin" SCADA dashboard built in Streamlit.
The Feature: A "Simulate Fan Failure" mechanism to demonstrate the model's predictive power in real-time.

High-Level SolutionApproach 
Summary
Multivariate Time-Series Forecasting: We are not just classifying "Safe vs. Unsafe." We are predicting the future curve of gas concentration.
Physics-Informed Logic: The model treats Airflow as a leading indicator and Methane as a lagging indicator.
Interactive Simulation: The system will allow users to manually manipulate input variables (fan speed) to see the predicted output change instantly.

## Key Technologies
Algorithm: Long Short-Term Memory (LSTM) networks or Facebook Prophet (for trend seasonality).
App Framework: Streamlit (Python).Data Handling: Pandas, NumPy.
InnovationMoving from Threshold-Based Monitoring (If $X > 5\%$, Alarm) to Trend-Based Prediction (If airflow drops, $X$ will be $> 5\%$ in 10 mins, Alarm Now).

## Project Phases 
Phase 1: Data Forensics & PreparationIngest the UCI Methane Dataset.Resample data (handle the high-frequency "every second" readings).Perform Correlation Analysis: Visualize the lag between Anemometer (wind) drops and Methane spikes.Deliverable: Cleaned dataframe ready for time-series supervised learning.

Phase 2: The "Expert" Model DevelopmentSplit data into Training (Normal Ops) and Testing (Accident/High Gas scenarios).Train LSTM/GRU models.Feature Engineering: Create "rolling window" features and "lag" features.Deliverable: A trained model file (model.h5 or .pkl) that takes current sensor data and outputs $T+10min$ predictions.

Phase 3: The Intelligence Layer & Simulation LogicDevelop the logic for the "Fan Failure" simulation.Create the "Warning Trigger": Logic that monitors the predicted values, not just current values.Deliverable: A Python script that can simulate a sudden drop in airflow data features.

Phase 4: The SCADA Dashboard (Streamlit)Visuals:Left Gauge: Ventilation Fan RPM (Green).Center Chart: Line chart (Blue = History, Red Dotted = Prediction).Action: Implement the "Simulate Fan Failure" button.Alerts: "EVACUATE SECTOR 4" banner overlay.Deliverable: Final Deployed MVP.

### Data Sources
#### Data Inputs
Anemometers: Main/Sub-main air current velocity ($m/s$).Methanometers: Methane concentration (%).Time: Timestamps for sequential analysis.

#### Data Source
Name: Methane Concentration in Underground Coal Mines.Repository: UCI Machine Learning Repository.Origin: Measured in a longwall coal mine in Poland.
### Stakeholders
Mine Safety Officers: Users of the dashboard.
Miners: Beneficiaries of the early warning.
Data Scientists: The builder of this logic

### Assumptions & Constraints
#### Assumptions
The relationship between airflow drop and methane rise is present in the dataset (physics holds true).
The UCI data is continuous enough to simulate a "live stream.

#### Constraints
Complexity: LSTMs can be computationally heavy; the dashboard must remain responsive.
Data Balance: The dataset may have far more "Safe" data than "Hazardous" data (requires careful training/resampling).

#### Risks & Mitigation
Risk: Model overreacts to small wind changes (False Alarms).
Mitigation: Smooth the input data (rolling averages) before feeding into the model.
Risk: "Fan Failure" simulation feels fake.
Mitigation: Ensure the "Failure" button modifies the input fed to the model, so the model genuinely predicts the spike based on its training.

### Tech Stack
Python 
Framework: TensorFlow/Keras (for LSTM) OR Facebook Prophet.
Data Processing: Pandas, Scikit-Learn (Scaler).
Visualization: Streamlit (App), Plotly (Interactive Charts).
DevOps: GitHub for version control.

#### Evaluation & Metrics
##### Performance Metrics
RMSE (Root Mean Squared Error): How close is the Red Dotted Line to reality?
Recall: Did we catch 100% of the high-methane events in the test set?

##### Validation Plan
Backtesting: Run the model on the last 20% of the UCI dataset "unseen" to verify predictive accuracy.Deployment Plan
Environment: Streamlit Cloud

The Demo: A SCADA-style interface where users can hit a "Simulate Fan Failure" button. This drops the virtual airflow, causing the AI to instantly project a red dotted line of rising gas levels and trigger an evacuation alert.

Tech Stack
Python
Streamlit
LSTM/Prophet.

### Success Criteria
When the "Simulate Fan Failure" button is clicked:The Green Gauge (Fan RPM) drops.
The Blue Line (Current Gas) stays steady for a moment (simulating lag).
The Red Dotted Line (Prediction) shoots up immediately.
The "EVACUATE" banner appears.

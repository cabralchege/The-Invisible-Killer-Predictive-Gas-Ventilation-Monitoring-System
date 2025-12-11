# The Invisible Killer: Predictive Ventilation & Gas Monitoring System

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue)](https://www.python.org/)
[![Streamlit](https://img.shields.io/badge/HMI-Streamlit-FF4B4B)](https://streamlit.io/)
[![XGBoost](https://img.shields.io/badge/Algorithm-XGBoost-green)](https://xgboost.readthedocs.io/)
[![License](https://img.shields.io/badge/License-MIT-purple)](LICENSE)

**A Prediction System for Underground Mine Safety.**

This project implements a **Predictive Control System** to forecast Methane ($CH_4$) gas concentrations **10 minutes into the future**. By analyzing real-time sensor telemetry (Airflow, Machinery Speed, Gas Levels) and applying fluid dynamics principles, the system provides a critical evacuation buffer before conditions become explosive.


## Table of Contents
- [System Overview](#-system-overview)
- [Operational Challenge](#-operational-challenge)
- [Engineering Architecture](#-engineering-architecture)
- [System Features](#-system-features)
- [Repository Structure](#-repository-structure)
- [Installation & Deployment](#-installation--deployment)
- [Validation & Performance](#-validation--performance)
- [HMI Dashboard](#-hmi-dashboard)


## System Overview

Underground mines rely on ventilation infrastructure to clear hazardous gases. Current industrial monitoring systems are **reactive**,  trigger alarms only *after* gas levels exceed safety thresholds (e.g., 1.0% or 2.0%).

This project shifts the paradigm to **Predictive Safety**, utilizing machine learning models to detect precursor anomalies before they escalate into critical failures.


## Operational Challenge

### The "Blind Spot"
If a ventilation fan fails or a cutter hits a gas pocket, Methane accumulation follows an exponential curve. Due to sensor latency and gas transit time, by the time a reactive sensor triggers an alarm, personnel may already be trapped in a zone with explosive potential.

### The Solution: 
We developed a forecasting engine that:
1.  Ingests live telemetry from SCADA sensors.
2.  Calculates physics-based risk coefficients.
3.  Forecasts gas concentrations **10 minutes ahead**.
4.  Triggers a pre-alarm if the trend indicates an imminent threshold breach.

## Engineering Architecture

The core logic is based on **Regression** within a safety-critical context.

| Component | Specification | Rationale |
| :--- | :--- | :--- |
| **Algorithmic Core** | **Gradient Boosted Trees (XGBoost)** | Selected for its ability to model non-linear "cliffs" (sudden pressure changes) better than Linear/Ridge methods. |
| **Physics Modeling** | **Risk Index** | Custom derived metric: $Risk = \frac{\text{Cutter Speed (V)}}{\text{Airflow (Q)}}$. Explicitly models the physical cause of gas pockets. |
| **Signal Processing** | **Log-Link Transformation** | Telemetry trained on $\log(y+1)$ to handle the exponential nature of gas release dynamics. |
| **Safety Logic** | **Cost-Sensitive Learning** | Dataset imbalance (99% Safe) was corrected using a **200x Penalty Weight** on missed critical events to ensure high reliability. |

---

## System Features

* **Physics-Aware Feature Engineering:** Incorporates signal inertia (Lags), intensity (Rolling Max-Pooling), and volatility metrics.
* **Zero-False-Negative Calibration:** The system uses a calibrated dynamic threshold (**0.17% predicted**) to capture **100%** of real-world 1.0% danger events.
* **Dashboard :** A dashboard allowing operators to simulate failure modes (e.g., Fan Efficiency Drop, Load Spikes) in a controlled environment.
* **Causal Diagnostics:** Provides real-time breakdown of contributing factors (e.g., "Airflow Efficiency Drop detected").


## Repository Structure

```bash
├── 1_research_lab.ipynb        # Engineering notebook: EDA, Physics Analysis, Baseline Testing
├── 2_production_pipeline.ipynb # Production Pipeline training & artifact generation
├── app.py                      # Deployed App : Streamlit Digital Twin Dashboard
├── ventilation_model_final.pkl # Serialized algorithmic model
├── requirements.txt            # System Dependencies
├── README.md                   # System Documentation
└── Data/
    └── resampled_methane_data.csv # Telemetry Data
    └── methane_data.csv        # Original methane dataset
    └── cleaned_methane_data.csv # The date and time merged into a timestamp column
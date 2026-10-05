# Building Behaviour Modelling for Indoor Relative Humidity Prediction

A data-driven building modelling project for predicting indoor relative humidity using multi-zone environmental measurements, energy-use data, and outdoor weather conditions.

The project applies time-series feature engineering, feature selection, and machine-learning regression models to capture relationships between indoor environmental conditions and building behaviour. Particular attention is given to temporally consistent model evaluation through time-series cross-validation.

## Project Overview

Indoor relative humidity is influenced by both internal building conditions and external weather. This project investigates whether indoor relative humidity can be predicted from a combination of:

- room-level temperature and humidity measurements;
- appliance and lighting energy use;
- outdoor temperature and humidity;
- atmospheric pressure, wind speed, visibility, and dew point;
- calendar and occupancy-related time features; and
- lagged indoor temperature and humidity variables.

The dataset covers approximately 4.5 months, from **11 January 2016 to 27 May 2016**, with measurements aggregated at **10-minute resolution**.

Indoor temperature and humidity were monitored using a **ZigBee wireless sensor network**, while energy consumption was recorded using M-Bus energy meters. Weather observations from the Chievres Airport weather station in Belgium were merged with the building measurements by timestamp.

## Objectives

The main objectives are to:

1. Explore relationships among indoor environmental, energy-use, and weather variables.
2. Engineer time-dependent and lagged features relevant to building behaviour.
3. Identify informative predictors using sequential feature-selection procedures.
4. Compare multiple regression and machine-learning approaches.
5. Evaluate model performance using time-series-aware validation.
6. Assess the effect of feature selection on predictive performance.

## Data

The building contains nine monitored indoor zones, including the kitchen, living room, laundry room, office, bathroom, ironing room, teenager room, and parents room.

### Main variables

| Category | Variables |
| --- | --- |
| Energy use | `Appliances`, `lights` |
| Indoor temperature | `T1`–`T9` |
| Indoor relative humidity | `RH_1`–`RH_9` |
| Outdoor/building-side conditions | `T6`, `RH_6` |
| Weather-station data | `To`, `Pressure`, `RH_out`, `Wind speed`, `Visibility`, `Tdewpoint` |
| Auxiliary variables | `rv1`, `rv2` |

The two random variables are retained as non-predictive reference attributes for model testing and feature-selection analysis.

## Time-Series Feature Engineering

The following additional features are created from the original measurements:

- hour of day;
- day of week;
- month;
- weekend indicator;
- working-hours indicator; and
- 20-minute lagged temperature and relative-humidity variables.

These features are designed to represent temporal patterns, occupancy-related behaviour, and delayed indoor environmental effects.

## Exploratory Analysis

A correlation matrix is used to examine the relationships among the available predictors and indoor environmental variables.

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/Correlation-Matrix.png"
       alt="Correlation matrix of building, energy, and weather variables"
       width="800">
</p>

## Modelling Workflow

Three regression approaches are evaluated:

- **Linear Regression**
- **Random Forest**
- **XGBoost**

Two modelling configurations are compared:

1. models trained using a selected subset of features; and
2. models trained without feature selection.

Model assessment uses temporally ordered cross-validation rather than random shuffling in order to preserve the time-series structure of the data.

### Evaluation metrics

Performance is assessed using:

- **MAE** — Mean Absolute Error
- **MSE** — Mean Squared Error
- **R²** — Coefficient of Determination
- **WAPE** — Weighted Absolute Percentage Error

## Feature Selection

A sequential feature-selection procedure is implemented using time-series cross-validation and WAPE as the selection criterion.

The main functions are:

- `WAPE(y_true, y_pred)`  
  Computes the Weighted Absolute Percentage Error.

- `acc_timeseriessplit(X, Y, model, cv)`  
  Computes average WAPE using time-series cross-validation.

- `feature_sel_1(model, Features, Target, Save_address=None, cv=10)`  
  Performs an initial stepwise feature-selection procedure.

- `feature_sel_2(model, Features, Target, FeatureSelection, Save_address=None, cv=10)`  
  Refines the selected feature set using an alternative evaluation order.

- `feature_sel_3(algorithm, Features, Target, FeatureSelection, Save_address=None, cv=10)`  
  Iteratively evaluates the remaining features and selects those producing the lowest WAPE.

### Feature-selection stages

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/selected_features_step1.png"
       alt="Selected features - step 1"
       width="800">
</p>

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/selected_features_step2.png"
       alt="Selected features - step 2"
       width="800">
</p>

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/selected_features_step3.png"
       alt="Selected features - step 3"
       width="800">
</p>

## Results

### Models with feature selection

| Model | MAE | MSE | R² |
| --- | ---: | ---: | ---: |
| Linear Regression | 1.21 | 2.85 | 0.7031 |
| Random Forest | 1.46 | 4.12 | 0.6577 |
| XGBoost | 1.27 | 3.09 | 0.6908 |

Among the feature-selected models, **Linear Regression** achieved the strongest validation performance and was therefore used for the corresponding test evaluation.

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-LR.png"
       alt="Linear Regression validation predictions with feature selection"
       width="800">
</p>

The selected Linear Regression model achieved the following test performance:

| Metric | Test value |
| --- | ---: |
| MAE | 1.48 |
| MSE | 4.28 |
| R² | 0.8404 |

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvT-LR.png"
       alt="Linear Regression test predictions with feature selection"
       width="800">
</p>

### Models without feature selection

| Model | MAE | MSE | R² |
| --- | ---: | ---: | ---: |
| Linear Regression | 0.96 | 2.14 | 0.7670 |
| Random Forest | 1.13 | 2.56 | 0.7162 |
| XGBoost | 1.04 | 2.19 | 0.7761 |

Among the models trained without feature selection, **XGBoost** achieved the highest validation R² and was selected for the corresponding test evaluation.

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-XG-nofeat.png"
       alt="XGBoost validation predictions without feature selection"
       width="800">
</p>

The selected XGBoost model achieved the following test performance:

| Metric | Test value |
| --- | ---: |
| MAE | 1.34 |
| MSE | 3.53 |
| R² | 0.8645 |

<p align="center">
  <img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvT-XG-nofeat.png"
       alt="XGBoost test predictions without feature selection"
       width="800">
</p>

## Key Findings

- Indoor relative humidity can be modelled with good predictive accuracy using a combination of building, weather, energy-use, temporal, and lagged variables.
- Time-series feature engineering provides a structured way to capture recurring and delayed effects in indoor environmental conditions.
- Feature selection does not necessarily improve predictive performance for every model.
- In this analysis, the best reported test performance was obtained by **XGBoost without feature selection**, with an **R² of 0.8645**.
- Linear Regression remained competitive, indicating that a substantial part of the relationship between the selected inputs and indoor relative humidity can be represented using a relatively simple model.

## Relevance

This project demonstrates experience in:

- data-driven building behaviour modelling;
- indoor environmental prediction;
- building and weather time-series analysis;
- sensor-data processing;
- temporal and lagged feature engineering;
- machine-learning regression;
- feature selection;
- time-series cross-validation; and
- quantitative model evaluation.

The workflow is relevant to applications in **building energy management, indoor-environment monitoring, smart buildings, digital twins, and data-driven building control**.

## Technologies and Methods

**Programming:** Python  
**Modelling:** Linear Regression, Random Forest, XGBoost  
**Data analysis:** time-series processing, correlation analysis, feature engineering  
**Validation:** time-series cross-validation  
**Metrics:** WAPE, MAE, MSE, R²

---

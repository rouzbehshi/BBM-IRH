# BBM-IRH
Building Behavior Modeling - Indoor Relative Humidity Prediction

This project aims to predict the Relative Humidity (RH) of a household using a dataset. The dataset contains observations of a 5 month period: 11 January 2016 to 27 May 2016. The dataset is a combination of measurements of humidity and temperature from households and weather data. The house has 9 residual zones and Humidity and temperature of it has been measured every 10 minutes

The data set is at 10 min for about 4.5 months. The house temperature and humidity conditions were monitored with a ZigBee wireless sensor network. Each wireless node transmitted the temperature and humidity conditions around 3.3 min. Then, the wireless data was averaged for 10 minutes periods. The energy data was logged every 10 minutes with m-bus energy meters. Weather from the nearest airport weather station (Chievres Airport, Belgium) was downloaded from a public data set from Reliable Prognosis (rp5.ru), and merged together with the experimental data sets using the date and time column. Two random variables have been included in the data set for testing the regression models and to filter out non predictive attributes (parameters). 

# Attribute Information

date time: year-month-day hour:minute:second.   
Appliances: energy use [Wh].   
lights: energy use of light fixtures in the house [Wh].   
T1: Temperature in kitchen area [Celsius].   
RH_1: Humidity in kitchen area [%].   
T2: Temperature in living room area [Celsius].   
RH_2: Humidity in living room area [%].   
T3: Temperature in laundry room area [Celsius].   
RH_3: Humidity in laundry room area [%].   
T4: Temperature in office room [Celsius].   
RH_4: Humidity in office room [%].   
T5: Temperature in bathroom [Celsius].    
RH_5: Humidity in bathroom [%].   
T6: Temperature outside the building (north side) [Celsius].   
RH_6: Humidity outside the building (north side) [%].   
T7: Temperature in ironing room [Celsius].   
RH_7: Humidity in ironing room [%].   
T8: Temperature in teenager room 2 [Celsius].   
RH_8: Humidity in teenager room 2 [%].   
T9: Temperature in parents room [Celsius].   
RH_9: Humidity in parents room [%].   
To: Temperature outside (from Chievres weather station) [Celsius].   
Pressure: (from Chievres weather station) [mm Hg].   
RH_out: Humidity outside (from Chievres weather station) [%].   
Wind speed: (from Chievres weather station) [in m/s].   
Visibility: (from Chievres weather station) [km].   
Tdewpoint: (from Chievres weather station) [Celsius].   
rv1: Random variable 1 [nondimensional].   
rv2, Random variable 2 [nondimensional].   

# Time-related features

1. Adding hour, day of the week, Month.  
2. Adding weekend flag.  
3. Adding the working hours.  
4. Adding 20-minute interval lagged columns of temperatures and relative humidities.  

# Visualizing And Exploring Dataset
**Correlation matrix:**

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/Correlation-Matrix.png"  alt="correlation matrix" title="correlation matrix" width="800" >
</p>

# ML with feature selection

## Feature extraction

```WAPE(y_true, y_pred)```: Weighted Absolute Percentage Error (WAPE) between two sets of values: y_true and y_pred.  
```acc_timeseriessplit(X,Y,model,cv)```: The average Weighted Absolute Percentage Error (WAPE) for a given model using time series cross-validation.  
```feature_sel_1 (model,Features,Target,Save_address=None,cv=10)```: This function performs a stepwise feature selection procedure based on the correlation between input features and the target variable. It aims to find the optimal set of features that minimizes the Weighted Absolute Percentage Error (WAPE) for a given model (model) using time series cross-validation.   
```feature_sel_2(model,Features,Target,FeatureSelection,Save_address=None,cv=10)```: This second step of feature selection aims to further refine the feature set by evaluating the features in a different order.  
```feature_sel_3(algorithm,Features,Target,FeatureSelection,Save_address=None,cv=10)```: The third step of feature selection aims to further refine the feature set by iteratively evaluating each remaining feature and selecting the one that yields the lowest WAPE value in each iteration. 

**Selected features 1**

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/selected_features_step1.png"  alt="selected feauture1" title="selected feauture1" width="800" >
</p>

**Selected features 2**

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/selected_features_step2.png"  alt="selected feauture2" title="selected feauture2" width="800" >
</p>

**Selected features 3**

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/selected_features_step3.png"  alt="selected feauture3" title="selected feauture3" width="800" >
</p>

## Linear regression (LR)

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-LR.png"  alt="RvV-LR" title="RvV-LR" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_LR | 1.21 |
| MSE_LR | 2.85 |
| R2 Score_LR | 0.7031 |
  
## Random Forest (RF)

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-RF.png"  alt="RvV-LRF" title="RvV-RF" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_LR | 1.46 |
| MSE_LR | 4.12 |
| R2 Score_LR | 0.6577 |

## XGboost

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-XG.png"  alt="RvV-XG" title="RvV-XG" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_LR | 1.27 |
| MSE_LR | 3.09 |
| R2 Score_LR | 0.6908 |

**Note:** Having analyzed the performance matrices, Linear regression is chosen as the promising model and the model is fitted for the test section.   

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvT-LR.png"  alt="RvT-LR" title="RvT-LR" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_LR | 1.48 |
| MSE_LR | 4.28 |
| R2 Score_LR | 0.8404 |

# ML without feature selection
## Linear regression (LR)

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-LR-nofeat.png"  alt="RvV-LR-nofeat" title="RvV-LR-nofeat" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_LR | 0.96 |
| MSE_LR | 2.14 |
| R2 Score_LR | 0.767 |

## Random Forest (RF)

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-RF-nofeat.png"  alt="RvV-RF-nofeat" title="RvV-RF-nofeat" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_LR | 1.13 |
| MSE_LR | 2.56 |
| R2 Score_LR | 0.7162 |

## XGboost

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvV-XG-nofeat.png"  alt="RvV-XG-nofeat" title="RvV-XG-nofeat" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_XG1 | 1.04 |
| MSE_XG1 | 2.19 |
| R2 Score_XG | 0.7761 |

**Note:** Having analyzed the performance matrices, XGboost is chosen as the promising model and the model is fitted for the test section.   

<p align="center">
<img src="https://github.com/rouzbehshi/BBM-IRH/blob/99ce849c3a3a0030690d3607e3a0574d9fc8a6a6/figures/RvT-XG-nofeat.png"  alt="RvT-XG-nofeat" title="RvT-XG-nofeat" width="800" >
</p>

**Performance metrics:**

| Metric | Value |
| :-: | :-: |
| MAE_XGT1 | 1.34 |
| MSE_XGT | 3.53 |
| R2 Score_XGT1 | 0.8645 |



 

   
    

# Predicting Duration of Power Outages in the US
Part of Project 5 for DSC80 at UCSD

## Framing the Problem

This site contains an exploration of a linear regression prediction problem using major power outage data in the continental U.S. from January 2000 to July 2016. The data was aquired from 
[this link](https://engineering.purdue.edu/LASCI/research-data/outages/outagerisks), and was compiled by Sayanti Mukherjee et. al. 

<strong> Prediction problem</strong>: Predict the severity (in terms of duration) of a major power outage.

The response variable is outage duration, in hours. This is interesting to analyze because being able to predict the duration of an outage given other information about the outage can help electricity providers manage their customers' expectations in the case of an outage.

The metric used to evaluate this model is RMSE. I used this rather than MAE because RMSE is in the same units as the response variable (hours). This makes it easier to understand the model's performance. 

The variables of interest to predict the response variable are:
- `U.S._STATE`
- `CAUSE.CATEGORY`
- `RES.PRICE`
- `TOTAL.PRICE`
- `POPDEN_URBAN`
- `AREAPCT_URBAN`
- `DEMAND.LOSS.MW`

These are all variables that an electricity provider would have access to at the onset of a power outage, and so can logically be used to predict the duration of the outage. 


## Baseline Model

For the baseline model, I am using `U.S._STATE`, a nominal categorical variable, and `DEMAND.LOSS.MW`, a quantitative variable. US State is OneHotEncoded and Demand Loss is standardized to make it more robust to outliers. Once encoded, these two features are put into a linear regression. 

The RMSE of the baseline model on the training data is 92.46. The RMSE of the baseline model on the test data is 83.73. In terms of outage duration, this performance could be better. An error of 83 hours is over 3 days which is a long time to be inconvenienced by a power outage. On the positive side, the RMSE was lower on the test data, suggesting good generalizability of the model. 


## Final Model

For the final model, I specified a series of different pipelines with different features (hyperparameters) that could predict outage duration better. I tried the following combinations of features in a linear regression:

- <strong>DEMAND.LOSS.MW standardized + OHE U.S._STATE and CAUSE.CATEGORY</strong>: the cause of the outage may be a very good indicator of outage duration because outages caused by severe weather, for example, may be more difficult to restore due to the unpredictability of the situation.  
- <strong>RES.PRICE + POPDEN_URBAN + DEMAND.LOSS standardized + OHE U.S._STATE</strong>: residential price and urban population could represent how wealthy the area is, and I expect that wealthier or more urban areas would have shorter outages. 
- <strong>TOTAL.PRICE + AREAPCT_URBAN + DEMAND.LOSS standardized + OHE U.S._STATE</strong>: I also wanted to try combinations with urban area percentage and the total price, in case these were better indicators of the wealth/demand in an area than population density or residential price. 


To determine which hyperparameters performed the best, I used k-fold cross validation with 10 folds in my training data. The combination of features with the least RMSE was DEMAND.LOSS.MW standardized with OHE U.S._STATE and CAUSE.CATEGORY. 

The RMSE of this final model on the training data was 87.43 and 72.94 on the test data. The lower RMSE on the test data again suggests good generalizability. Compared to the baseline model, the final model improved error by about 11 hours. I think this is a significant improvement. 

## Fairness Analysis


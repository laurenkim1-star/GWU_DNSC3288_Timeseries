# GWU_DNSC3288_Timeseries
Final project for DNSC 3288 - Big Data, Predictive Analytics, and Ethics.

# Predicting Future Sales Model Card
### Basic Information
* **Person or organization developing model**:
- Lauren Kim: `lauren.kim1@gwmail.gwu.edu`,
- Kaylyn Phung: 'kaylyn.phung@gwmail.gwu.edu',
- Aaron Park: 'suyong848@gwmail.gwu.edu',
- Tony Yue: 'yyue53@gwmail.gwu.edu'


* **Model date**: December, 2025
* **Model version**: 1.0
* **License**: Apache 2.0
* **Model implementation code**: [Project.ipynb] (Insert .ipynb link)

### Intended Use
* **Primary intended uses**: This model provides an *example* of a predicting future sales using XGBoost timeseries model 
* **Primary intended users**: Students in GWU's Business Analytics program.
* **Out-of-scope use cases**: Any use beyond an educational example is out-of-scope.

### Training Data

* Data dictionary: 

| Name | Modeling Role | Measurement Level| Description|
| ---- | ------------- | ---------------- | ---------- |
| **date** | input feature | Ordinal | Original calendar date of each daily sales record |
| **date_block_num** | input feature | Ordinal | month index representing time |
| **shop_id** | input feature | Categorical | unique identifier for each shop |
| **item_id** | input feature | Ordinal | Unique identifier for each product item |
| **item_price** | input feature | Numerical | price of the item in the transaction |
| **item_name** | input feature | Categorical | name of items |
| **item_category_id** | input feature | Categorical | categorical identifier of each product’s category |
| **shop_name** | input feature | Categorical | name of the shops in the data set |
| **item_category_name** | input feature | Categorical | name of each category which items belong to |

* **Source of training data**: Kaggle, https://www.kaggle.com/c/competitive-data-science-predict-future-sales 
* **How training data was divided into training and validation data**: 
* **Number of rows in training and validation data**:
  * Training rows: 200,327
  * Validation rows: 22,259
 
### Test Data
* **Source of test data**: Kaggle, https://www.kaggle.com/c/competitive-data-science-predict-future-sales 
* **Number of rows in test data**: 214,200
* **State any differences in columns between training and test data**: Compared to the train data, test data only contains 3 columns, including ID, shop ID, and item ID. Since test data does not contain any information related to sales, predictions are purely generated based on the model and its input features. 

### Model details
* **Columns used as inputs in the final model**: 
* **Column(s) used as target(s) in the final model**: 
* **Type of model**: XGBoost Regressor
* **Software used to implement the model**: Python, scikit-learn
* **Version of the modeling software**: 3.13.5
* **Hyperparameters or other settings of your model**: 
```
model = XGBRegressor(
    max_depth=8,
    n_estimators=1000,
    min_child_weight=10,
    colsample_bytree=0.8,
    subsample=0.8,
    eta=0.05,
    seed=42,
    eval_metric="rmse", early_stopping_rounds=10
)

```

### Quantitative Analysis

* Models were assessed primarily with RMSE and MAE. See details below:

| Train RMSE | Validation RMSE | Test RMSE |
| ------ | ------- | -------- |
| 0.8090 | 0.9071 | 0.8316 |

| Train MAE | Validation MAE | Test MAE |
| ------ | ------- | -------- |
| 0.3057 | 0.3132 | 0.3000 |

* Segmented Error Analysis:
<img width="1460" height="853" alt="image" src="https://github.com/user-attachments/assets/7e699915-4367-4d3a-8ba5-4c2a35e875ff" />

- Rows = Shop IDs
- Columns = Item Category IDs
- Color = RMSE for shop & category combination
  - Dark purple = low error (model predicts well)
  - Green & yellow = high error (model predicts poorly)
  - Straight vertical lines of yellow = certain categories are hard everywhere
  - Straight horizontal lines = certain shops perform poorly across all categories
  - Bright squares = extreme outliers (model failed completely)

* Interpretation:
    * Bright yellow vertical bands indicate that the model lacks features strong enough to capture explosive sales patterns.
    * Bright horizontal bands indicate that the model underperforms for certain shops across every category, displauing a structural issue.
         * These shops sell more unpredictable items, with a large diversity.
    * Dark purple waves indicate that the XGBoost model excels with categories with stable demand, with more predictable monthly sell-through.
   


### Ethical Considerations

**Potential negative impacts of the model**:
Math or software problems: 
  * Overfitting despite regularization:XGBoost can still memorize noise in the training data, especially when many engineered features or time-dependent patterns are included.  This inflates validation accuracy and creates overly confident forecasts that could mislead decision-makers.
* Data leakage risks:
  * If any future information (e.g., lag features created improperly, cumulative metrics, or look-ahead variables) leaks into the training window, the model will appear accurate but fail in production.
* Sensitivity to missing data or preprocessing errors:
  * Even small inconsistencies in scaling, encoding, or date alignment can shift predictions significantly. In a real retail setting, failing to handle missing entries or sudden outliers can lead to biased demand forecasts.
Real-world Risks:
* Inventory Misallocation
  * Who: Store managers, supply-chain teams, customers.
  * What: Over/under-ordering due to inaccurate demand predictions.
  * When: Peak seasons, holidays, promotions, or unexpected shifts in demand.
  * How:
     Overstock → waste, higher storage costs.
     Understock → lost revenue, customer dissatisfaction.
* Unequal Treatment Across Stores
  * Who: Smaller stores, rural regions, minority communities.
  * What: Systematic underprediction or overprediction of sales.
  * When: When historical data is uneven or certain store types are underrepresented.
  * How:
     Less inventory and staffing for wrongly “low-priority” stores.
     Reinforces structural inequities in the firm’s operations.
* Strategic Misalignment
  * Who: Corporate decision-makers and financial planners.
  * What: Misleading forecasts distort budget planning, staffing, hiring, and marketing.
  * When: When leadership uses model forecasts for quarterly or annual planning.
  * How:
     Misallocation of capital.
     Incorrect financial assumptions.
     Poor strategic outcomes.

**Potential uncertainties relating to impacts of using this model**:
Math or software problems 
* Non-stationary data:
  * Consumer behavior, price sensitivity, and promotional effects change over time. XGBoost assumes the future resembles the past, which introduces uncertainty if structural shifts occur (e.g., economic shocks, pandemics).
* Feature importance instability:
  * XGBoost’s split patterns may change drastically with small updates to the dataset. This makes it uncertain whether the model is truly learning persistent causal signals or merely capturing temporary correlations.
* Hyperparameter uncertainty:
  * Boosting models depend heavily on tuning choices (learning rate, depth, subsampling). Slight deviations could materially affect predictions, making long-term reliability uncertain.
* Propagated noise:
  * Lag features or rolling statistics can accumulate historical errors, creating uncertainty that compounds over time.

Real-world risks: 
* Who:
  * The real-world risks of this model can affect businesses utilizing time series to predict future sales for inventory control or projected revenue.
What: Risks come in the form of financial loss. This model predicts the future sales of a business based on past behavior. Many external factors may affect the accuracy of this model (ie. Economic downturn). 
* When:
  * As models are continually deployed, their risks can never be fully eliminated. Once a model is in production and generating predictions, performance assessment must estimate how well it will generalize to new data to gauge its real-world effectiveness. And because ML systems are complex, failures or attacks are difficult to anticipate or prevent.
* How:
  * Over-reliance on this model can lead to financial miscalculations. If a retail business were to base its inventory stocking on this model, the result could be financial loss due to inaccurate projections. This model is not 100% accurate in estimating future sales, but it can be used as a rough estimate of what future sales could look like.

Unexpected Results:
* Various unforeseen or unusual tendencies were noticed during the assessment of the prediction and residual trends of the model. These oddities do not necessarily suggest that the model is broken but it points out areas in which XGBoost would perform poorly because of inherent constraints of the data and the prediction problem.
* Concentration at Both ends (Q-Q Plot Behavior):
<img width="543" height="545" alt="image" src="https://github.com/user-attachments/assets/18e7e7ee-f82e-4c9c-af2c-5e57912714f6" />

  * The Q-Q plot indicates that there is a major deviation of the theoretical normal line especially in the two tails. This implies that the model generates extremely huge positive or negative errors on a small fraction of items.
  * These drastic residuals are mainly caused by:
    * Spike at the season (e.g. holiday sales)
    * Products that have small historical information
    * Items that have ended and the historic data are giving a false impression of demand
    * The fat tails indicate that XGBoost is incapable of capturing all unusual sudden shifts that adhere to a real time-series data.

Overweight Clustering at Zero and Near-Zero Predictions:
* Even in cases where true sales are much higher, a high proportion of predictions between 0 and 1 gives a high probability of occurrence.
* The reason is that the dataset is grossly lopsided (most item/shop/month combinations report zero or very low sales), which results in:
  * Preference to safe low-prediction model
  * Low forecasting the popular goods
* This is observed evidently in the Residuals vs. Predicted plot, in which a significant number of predictions are close to zero with large positive residuals.

Possible Excessive Smoothing of Large Volume Products:
* When the items are selling 10-20 items per month, the model is more likely to come closer to the mean (around 6-10).
* This gives the shape of a funnel to the residuals:
    * Predictive values are large and negative - higher predicted values
    * Low values of the prediction - positive large residues
    * This trend means that the model can be agile to the variability of high-selling products, probably due to:

Means of features increase damping behavior of unique items:
* The tree-based models smooth prediction of similar items.
* Fluctuating Behavior with Price Change Characteristics.
  * The importance of some features (e.g., delta_price_lag) is very high.

In case of an abrupt price fall or rise of an item:
* The model is also hyper-sensitive, over-reacting on the price signal, which is not as significant as it is perceived to be.
* This sometimes leads to high predictions which are not the same as the historical sales.
* Such anomalies can be seen in the scatter plots where some of the prediction is going to be unexpected even when low selling items are involved.


Mismatches between Real-World Behavior and Validation RMSE
* Although the validation RMSE seems to be good (0.9068), it might not be the same in actual performance due to the following reasons:
* The Black Friday and early holiday season effects are on the Kaggle test month.
* The model is not specifically based on seasonal or promotional characteristics.
  * This implies that the model can appear to be good on paper and yet fail to work in an operational environment.


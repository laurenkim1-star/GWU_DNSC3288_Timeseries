# GWU_DNSC3288_Timeseries_Group06
Final project for DNSC 3288 - Big Data, Predictive Analytics, and Ethics.

# Predicting Future Sales Model Card
### Basic Information
* **Person or organization developing model**:
  - Lauren Kim: `lauren.kim1@gwmail.gwu.edu`,
  - Kaylyn Phung: `kaylyn.phung@gwmail.gwu.edu`,
  - Aaron Park: `suyong848@gwmail.gwu.edu`,
  - Tony Yue: `yyue53@gwmail.gwu.edu`


* **Model date**: December, 2025
* **Model version**: 1.0
* **License**: Apache 2.0
* **Model implementation code**: [[Group06-Model-Code.ipynb](https://drive.google.com/file/d/1l-J4At2HyMEfatVnPbTIVWAQv1_ux0FA/view?usp=sharing)]

### Intended Use
* **Primary intended uses**: This model provides an *example* of a predicting future sales using XGBoost time series model 
* **Primary intended users**: Students in GWU's Business Analytics program.
* **Out-of-scope use cases**: Any use beyond an educational example is out-of-scope.

### Training Data

* Data dictionary: 

| Name | Modeling Role | Measurement Level| Description|
| ---- | ------------- | ---------------- | ---------- |
| **date** | Input feature | Ordinal | Original calendar date of each daily sales record |
| **date_block_num** | Input feature | Ordinal | Month index from 0-33 representing time progression to capture temporal trends |
| **shop_id** | Input feature | Categorical | Unique identifier for each shop, encoded as integer but categorical |
| **item_id** | Input feature | Categorical | Unique identifier for each product, used to model item-specific demand |
| **item_cnt_month** | Target feature | Continuous | Monthly sales count for item-shop pairs, values have been clipped to [0,20] |
| **city_code** | Input feature | Categorical | Encoded city label extracted from shop name |
| **item_category_id** | Input feature | Categorical | Categorical identifier of each product’s category |
| **type_code** | Input feature | Categorical | Item type derived from item category name |
| **subtype_code** | Input feature | Categorical | Subcategory of item in the transaction |
| **item_cnt_month_lag_1, 2, 3, 6, & 12** | Input feature | Continuous | Price of the item in the transaction 1, 2, 3, 6 & 12 months ago |
| **date_avg_item_cnt_lag_1** | Input feature | Continuous | Average item sales across all shops last month |
| **date_item_avg_item_cnt_lag_1, 2, 3, 6 & 12** | Input feature | Continuous | Average sales for an item 1, 2, 3, 6 & 12 months ago |
| **date_shop_avg_item_cnt_lag_1, 2, 3, 6 & 12** | Input feature | Continuous | Average shop sales of all items 1, 2, 3, 6 & 12 months ago  |
| **date_cat_avg_item_cnt_lag_1** | Input feature | Continuous | Category-level average sales 1 month ago |
| **date_shop_cat_avg_item_cnt_lag_1** | Input feature | Categorical | Average sales for shop-category pair 1 month ago |
| **date_city_avg_item_cnt_lag_1** | Input feature | Categorical | Average sales of all shops in a city 1 month ago |
| **date_item_city_avg_item_cnt_lag_1** | Input feature | Categorical | cAverage sales of an item in a city 1 month ago |
| **delta_price_lag** | Input feature | Categorical | Relative price change from last sale against hisorical average price |
| **month** | Input feature | Ordinal | Month index (1-12) |
| **days** | Input feature | Continuous | Number of days within a month |
| **item_shop_last_sale** | Input feature | Continuous | How many months since item was last sold in a shop |
| **item_last_sale** | Input feature | Continuous | How many months since this item last sold anywhere |
| **item_shop_first_sale** | Input feature | Continuous | How many months since this item first appeared in a shop |
| **item_first_sale** | Input feature | Continuous | How many months since this item first appeared |


* **Source of training data**: Kaggle, https://www.kaggle.com/c/competitive-data-science-predict-future-sales 
* **How training data was divided into training and validation data**: The training data was dividided using time-based splitting, rather than random splitting as this is a time series model. The training data included all months from 0-32, while the validation data only included month 33. 
* **Number of rows in training and validation data**:
  * Training rows: 6,186,922
  * Validation rows: 238,172
 
### Test Data
* **Source of test data**: Kaggle, https://www.kaggle.com/c/competitive-data-science-predict-future-sales 
* **Number of rows in test data**: 214,200
* **State any differences in columns between training and test data**: The train data and test data dataframes used as inputs into the final model use the same columns.

### Model details
* **Columns used as inputs in the final model**: All columns discluding item_cnt_month were used as inputs into the final model. 
* **Column(s) used as target(s) in the final model**: item_cnt_month
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

* Models were assessed primarily with RMSE, MAE and segmented error analysis. See details below:

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
    * Bright yellow vertical bands indicate that the model lacks features strong enough to capture outlier sales patterns.
    * Bright horizontal bands indicate that the model underperforms for certain shops across every category, displaying a structural issue.
         * These shops sell more unpredictable items, with a large diversity.
    * Dark purple waves indicate that the XGBoost model excels with categories with stable demand, with more predictable monthly sell-through.
   


### Ethical Considerations

**Potential negative impacts of the model**:

Math or software problems: 
* Overfitting despite regularization:
    * XGBoost can still memorize noise in the training data, especially when many engineered features or time-dependent patterns are included. This inflates validation accuracy and creates overly confident forecasts that could mislead decision-makers.
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
     Overstock leads to waste, higher storage costs.
     Understock leads to lost revenue, customer dissatisfaction.
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
* Various unforeseen or unusual tendencies were noticed during the assessment of the prediction and residuals of the model, however the oddities do not necessarily suggest that the model is broken but it points out areas in which XGBoost would perform poorly because of inherent constraints of the data and the prediction problem.
* Concentration at Both ends (Q-Q Plot Behavior):
<img width="543" height="545" alt="image" src="https://github.com/user-attachments/assets/18e7e7ee-f82e-4c9c-af2c-5e57912714f6" />

  * The Q-Q plot indicates that there is a major deviation of the theoretical normal line especially in the two tails, implying that the model generates extremely huge positive or negative errors on a small fraction of items.
  * These drastic residuals are mainly caused by:
    * Spike at the season (e.g. holiday sales).
    * Products that have small historical information.
    * Items that have ended and the historic data are giving a false impression of demand.
    * The fat tails indicate that XGBoost is incapable of capturing all unusual sudden shifts that adhere to a real time-series data.

Overweight Clustering at Zero and Near-Zero Predictions:
* Even in cases where true sales are much higher, a high proportion of predictions between 0 and 1 gives a high probability of occurrence.
* The reason is that the dataset is grossly lopsided (most item/shop/month combinations report zero or very low sales), which results in:
  * Preference to safe low-prediction model.
  * Low forecasting the popular goods.
* This is observed evidently in the Residuals vs. Predicted plot, in which a significant number of predictions are close to zero with large positive residuals.
<img width="703" height="468" alt="image" src="https://github.com/user-attachments/assets/d8d6f29e-077f-4b36-9a8e-fd085856e288" />


Possible Excessive Smoothing of Large Volume Products:
* When the items are selling 10-20 items per month, the model is more likely to come closer to the mean (around 6-10).
* This gives the shape of a funnel to the residuals:
    * Predictive values are large and negative - higher predicted values.
    * Low values of the prediction - positive large residues.
    * This trend means that the model can be agile to the variability of high-selling products.

Means of features increase damping behavior of unique items:
* The tree-based models smooth prediction of similar items.
* Fluctuating Behavior with price change features.
  * The importance of some features (e.g., delta_price_lag) is very high.
<img width="1070" height="1161" alt="image" src="https://github.com/user-attachments/assets/8597ae04-f059-4f66-bf6c-adbfbeb5200c" />



In case of an abrupt price fall or rise of an item:
* The model is also hyper-sensitive, over-reacting on the price signal, which is not as significant as it is perceived to be.
  * This sometimes leads to high predictions which are not the same as the historical sales.
* Such anomalies can be seen in the scatter plots where some of the prediction is going to be unexpected even when low selling items are involved.


Mismatches between Real-World Behavior and Validation RMSE:
* Although the validation RMSE seems to be good (0.9068), it might not be the same in actual performance due to the following reasons:
  * The Black Friday and early holiday season effects are on the Kaggle test month.
  * The model is not specifically based on seasonal or promotional characteristics.
 
NOTE: ChatGPT-5 was utilized in the analysis of this model. LLM usage within the attached Python notebook has been annotated.

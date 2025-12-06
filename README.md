# GWU_DNSC3288_Timeseries
Final project for DNSC 3288 - Big Data, Predictive Analytics, and Ethics.

# Predicting Future Sales Model Card
### Basic Information

* **Person or organization developing model**: Lauren Kim, `lauren.kim1@gwmail.gwu.edu`
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
| **date** | input feature |  | original calendar date of each daily sales record |
| **date_block_num** |   |  | |
| **shop_id** | |  |  |
| **item_id** |  | |  |
| **item_price** |  |  |  |
| **item_name** |  |  |  |
| **item_category_id** |  |  |  |
| **shop_name** |  |  |  |
| **item_category_name** |  |  |  |

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

### Ethical Considerations

* **Potential negative impacts of the model**:
* 
* **Potential uncertainties relating to impacts of using this model**:
*  
*  

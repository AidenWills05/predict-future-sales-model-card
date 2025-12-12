# Model Card: Predict Future Sales

## Basic Information
**Project**: Predict Future Sales (Kaggle “Competitive Data Science: Predict Future Sales” competition)  
**Author**: Aiden Williams (aiden.williams@gwu.edu)  
**Date**: November 30, 2025  
**Model version**: v1.0  
**License**: MIT License  
**Model implementation code**: [https://www.kaggle.com/code/aidenwilliams020/notebookd3ab0d0b81](https://www.kaggle.com/code/aidenwilliams020/aiden-williams-predict-sales)

---

## Intended Use
**Intended uses:**
- Educational demonstration of how to build a simple time series forecasting model using monthly retail sales data  
- Practice with data preparation, lag features, regression modeling and model evaluation  

**Intended users:**
- Students, instructors and beginners studying forecasting or machine learning  

**Out of scope uses:**
- Real retail forecasting  
- Inventory planning  
- Financial decisions or systems that affect customers or operations  

---

## Training Data
**Source of training data:**  
Kaggle “Predict Future Sales” sales_train.csv file.

**How training data was divided:**
- Rows with valid lag and date_block_num less than 33 were used for training  
- Rows with date_block_num equal to 33 were the validation set  
- The final model was refit using all rows with non missing lag  

**How the lag feature was created:**  
The data was sorted by shop_id, item_id and date_block_num. Then item_cnt_month was shifted by one month within each group to form item_cnt_month_lag1.

**Number of rows:**
- Training rows (months less than 33 with valid lag): about 1.15 million  
- Validation rows (month 33): about 34 thousand  
- Final full training rows for refit: about 1.18 million  

Data dictionary (training data after aggregation to monthly level):

| Column              | Modeling role | Measurement level       | Description                                                          |
|---------------------|--------------|--------------------------|----------------------------------------------------------------------|
| date_block_num      | Feature      | Numeric (integer)        | Month index from 0 to 33 representing time since Jan 2013           |
| shop_id             | Feature      | Categorical (integer ID) | Location (shop) where the item was sold                             |
| item_id             | Feature      | Categorical (integer ID) | Unique item identifier                                               |
| item_cnt_month      | Target       | Numeric (continuous)     | Total units of the item sold in that shop during that month         |
| item_cnt_month_lag1 | Feature      | Numeric (continuous)     | Previous month’s item_cnt_month for each shop–item combination      |


---

## Test Data
**Source of test data:**  
Kaggle test.csv file.

**Number of rows:**  
214,200 rows.

**Differences in columns:**
- Test data contains an ID column that training data does not have  
- Test data does not have item_cnt_month  
- Lag values were merged in from the October 2015 monthly totals  
- Aside from the synthetic ID and missing target the structure matches the monthly training table  

---

## Model Details
**Input columns used in the final model:**  
item_cnt_month_lag1  

**Target column:**  
item_cnt_month  

**Type of model:**  
Linear Regression (single variable regression)

**Software used:**  
Python  
pandas  
scikit-learn  

**Version numbers:**  
Python 3.10  
pandas 2.0  
scikit-learn 1.3  

**Hyperparameters or settings:**  
LinearRegression default settings:  
- fit_intercept = True  
- copy_X = True  
- n_jobs = None  
- positive = False  

**Additional experimentation:**  
A RandomForestRegressor using the lag feature, date_block_num and a non zero lag indicator was tested. It produced a higher validation RMSE than the linear regression and was not selected.

---

## Quantitative Analysis

### Metrics used
The Kaggle competition uses RMSE on item_cnt_month.  
RMSE was used for training, validation and test.

### Results
**Training RMSE:**  
6.052667207299196  

Metrics summary:

| Dataset               | Metric | Value    |
|-----------------------|--------|---------:|
| Validation (month 33) | RMSE   | ~14.13   |
| Kaggle public test    | RMSE   | ~1.12918 |
| Kaggle private test   | RMSE   | ~1.12705 |
 

### Interpretation
The baseline one lag linear regression performs reasonably well and is close to typical public benchmark scores. It underfits high volume items and does not model seasonality or item trends. More advanced feature engineering or models like LightGBM, XGBoost or neural nets would perform better.

---

## Plots Included
**Histogram of item_cnt_month:**  
Shows the heavy right skew in monthly sales with many zeros and a few large values.

**Scatter plot of predicted vs actual sales (validation month 33):**  
Displays predictions compared to true values with a red diagonal reference line. Most points cluster near zero and larger sales amounts are often underpredicted.

<img width="457.5" height="300" alt="Screenshot (280)" src="https://github.com/user-attachments/assets/53e3aa7e-7488-4375-90e4-29f816b985c9" />
<img width="336" height="300" alt="Screenshot (279)" src="https://github.com/user-attachments/assets/a5dabb8f-c677-458f-beee-bf9a49bc6d48" />

---

## Ethical Considerations
**Potential negative impacts**
- The model ignores promotions, seasonality, pricing changes or external economic factors  
- If used in a real store it could cause stockouts or oversupply  
- It learns only from past behavior which can reinforce existing stocking patterns  

**Potential uncertainties**
- Sales patterns can change suddenly due to holidays, releases or trend shifts  
- Different stores behave differently and none of those factors are included  
- Noise is high for low volume items which increases errors  

**Unexpected results**
- The RandomForestRegressor performed worse than simple linear regression  
- For many items last month’s sales alone explained most of the variation  

---

## AI Use

I used AI tools (Perplexity / ChatGPT-style assistant) to help:
- Debug and structure Python code in the Kaggle notebook.
- Design the modeling workflow (monthly aggregation, lag features, and baseline vs. alternative model).
- Help with structuring of this model card.

All code and text were reveiwed, run, and edited by me to ensure they match the project requirements. The AI assistance helped me work faster, avoid bugs, and better explain my methods and results.


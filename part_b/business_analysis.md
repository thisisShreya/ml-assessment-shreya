# Part B: Business Case Analysis

## B1. Problem Formulation
### (a) Machine Learning Problem Formulation

The business objective is to determine which promotion should be deployed in each store each month in order to maximise the number of items sold.

The **target variable** in this problem is **items sold**, which represents the total number of products purchased in a given store during a promotion period.

The **candidate input features** include:

- Store location type (urban, semi-urban, rural)
- Store size
- Monthly footfall
- Competition density
- Customer demographics
- Promotion type
- Historical sales trends
- Seasonal factors (month, festivals, holidays)
- Previous promotion performance

This is best formulated as a **supervised learning regression problem**.

The reason is that the target variable, items sold, is a continuous numerical value rather than a category. Since the goal is to predict the expected sales volume under different promotional strategies, regression is more suitable than classification.

A regression model can estimate the likely number of items sold for each promotion-store combination, allowing the company to choose the promotion with the highest predicted outcome.

---

### (b) Why Items Sold is a Better Target Variable than Total Sales Revenue

Using **items sold** as the target variable is more reliable than using **total sales revenue** because revenue can be influenced by many external factors beyond promotion effectiveness.

For example:

- Higher-priced items generate more revenue even if fewer units are sold.
- Revenue may fluctuate due to pricing changes, inflation, or product mix.
- Discounts may reduce revenue while still increasing customer engagement and sales volume.

In contrast, items sold directly measures how effectively a promotion encourages purchasing behaviour.

Since the business goal is to maximise customer response to promotions, sales volume provides a clearer and more consistent indicator of promotional success.

This illustrates a broader principle in real-world machine learning projects: **the target variable should align closely with the true business objective**.

Choosing the wrong target can lead to misleading conclusions and suboptimal decisions. A carefully selected target ensures that the model optimises for what the business genuinely values.

---

### (c) Alternative Modelling Strategy

Instead of building one single global model across all 50 stores, a better approach would be to use a **segmented modelling strategy**.

This involves grouping stores based on key characteristics such as:

- Location type
- Customer demographics
- Store size
- Competition density

Separate models can then be trained for each segment.

For example:

- One model for urban stores
- One model for semi-urban stores
- One model for rural stores

This approach is more effective because customer behaviour and promotion response differ significantly across store types.

A promotion that performs well in urban areas may not have the same effect in rural locations.

By accounting for these differences, segmented models can capture local patterns more accurately and produce better predictions.

This strategy improves decision-making by ensuring promotions are tailored to the unique characteristics of each store group.

## B2. Data and EDA Strategy
### (a) Joining Data Sources and Defining the Modelling Dataset

To build an effective machine learning model, the four raw tables must first be combined into a single modelling dataset.

The four tables are:

- **Transactions**
- **Store attributes**
- **Promotion details**
- **Calendar data**

The **transactions** table would act as the primary fact table because it contains the core sales activity.

The joining process would be:

1. Join **transactions** with **store attributes** using `store_id`
2. Join **transactions** with **promotion details** using `promotion_id` or promotion type
3. Join **transactions** with **calendar data** using `transaction_date`

This creates a complete view of each transaction enriched with contextual business information.

The **grain of the final modelling dataset** should be:

**One row = one store for one month under one promotion condition**

This monthly store-level structure is suitable because the business decision is made monthly for each store.

Before modelling, transaction-level data should be aggregated into store-month summaries.

Key aggregations include:

- Total items sold
- Total revenue
- Average basket size
- Number of transactions
- Promotion usage counts
- Average competition density
- Monthly footfall estimates

This aggregation ensures the dataset reflects decision-making at the same level as business operations.

---

### (b) Exploratory Data Analysis Strategy

Before building a model, several EDA steps should be performed.

#### 1. Promotion Performance Comparison

A bar chart comparing average items sold across promotion types would reveal which promotions generally perform best.

This helps identify whether certain promotions consistently drive higher sales and whether promotion type should be treated as a strong predictor.

---

#### 2. Store Location Analysis

A grouped chart showing sales by promotion type across urban, semi-urban, and rural stores would highlight location-specific behaviour.

If performance varies greatly by location, this suggests interaction features or segmented models may be necessary.

---

#### 3. Seasonal Trend Analysis

A time-series plot of monthly sales across all stores would show seasonal demand patterns.

This can reveal effects of holidays, festivals, and month-end behaviour, guiding time-based feature engineering.

---

#### 4. Correlation Heatmap

A heatmap of numerical features would identify relationships between variables such as footfall, competition density, and sales.

Highly correlated features may indicate redundancy, while strong predictors can guide feature selection.

---

The findings from these analyses would influence decisions such as:

- creating interaction features
- adding seasonality indicators
- segmenting stores
- removing redundant variables

---

### (c) Handling Promotion Imbalance

If 80% of transactions occurred without any promotion, the dataset is imbalanced.

This imbalance can cause the model to learn patterns mainly from non-promotion periods and underperform when predicting promotion effectiveness.

As a result, recommendations for promotional strategies may become unreliable.

To address this issue, I would:

- apply balanced sampling techniques
- use weighting strategies during training
- ensure evaluation includes promotion-specific performance checks
- consider modelling promotion and non-promotion periods separately

These steps help the model better understand the impact of promotional events rather than being dominated by baseline behaviour.

---

## B3. Model Evaluation and Deployment
### (a) Train-Test Split and Evaluation Metrics

Since the dataset contains time-based monthly records over three years, the train-test split should be chronological.

For example:

- First 80% of months → training set
- Most recent 20% of months → test set

This simulates real-world forecasting where future periods must be predicted using only past data.

A random split is inappropriate because it can introduce data leakage by allowing future information into the training process.

This would create overly optimistic performance estimates.

The evaluation metrics I would use are:

#### RMSE (Root Mean Squared Error)

Measures average prediction error while penalising larger mistakes more heavily.

In this context, it shows how far predicted sales volumes deviate from actual values.

---

#### MAE (Mean Absolute Error)

Measures the average absolute difference between predictions and actual values.

This is easier for business teams to interpret because it represents average units sold error.

---

#### R² Score

Indicates how much variance in sales volume is explained by the model.

A higher value suggests stronger predictive power.

Together, these metrics provide a balanced understanding of model accuracy and reliability.

---

### (b) Explaining Different Recommendations Using Feature Importance

If the model recommends different promotions for the same store in different months, feature importance can help explain why.

Feature importance measures how strongly each input variable influences predictions.

For Store 12:

- In December, seasonal features such as festival periods and increased shopping demand may make Loyalty Points Bonus more effective.
- In March, lower seasonal demand or different customer behaviour may favour Flat Discount.

To investigate, I would review:

- feature importance scores
- month-specific inputs
- historical performance of promotions in similar conditions

This analysis would show which variables changed between months and how they influenced recommendations.

When communicating to the marketing team, I would explain that recommendations differ because customer behaviour is context-dependent, and the model adapts decisions based on time, season, and local conditions.

---

### (c) Deployment Process

The trained model should be saved after development so it can be reused without retraining each month.

I would save the model using tools such as `joblib` or `pickle`.

This preserves:

- preprocessing steps
- feature engineering logic
- trained parameters

At the start of each month:

1. Collect new monthly store data
2. Apply the same preprocessing pipeline
3. Generate predictions for all stores
4. Recommend the promotion with the highest expected items sold

This creates a repeatable production workflow.

To monitor performance, I would track:

- prediction errors over time
- shifts in feature distributions
- recommendation acceptance rates
- business outcomes after deployment

If performance declines significantly or data patterns change, retraining should be triggered.

This ensures the model remains relevant as market conditions evolve.

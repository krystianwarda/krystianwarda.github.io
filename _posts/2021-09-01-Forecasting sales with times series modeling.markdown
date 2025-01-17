---
layout: post
title: Sales forecasting with times series data - Naive Forecasting, Gradient Boosting, XGBoost, Random Forest 
date: 2021-11-01 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: sales.jpg # Add image post (optional)
tags: [forecasting, python] # add tag
---

Time series forecasting occurs when you make scientific predictions based on historical time stamped data. 
It involves building models through historical analysis and using them to make observations and drive future strategic decision-making.

<br>
<br>

Code can be viewed at following github page:
<br>
<a href="
https://github.com/krystianwarda/Sales-forecasting">
https://github.com/krystianwarda/Sales-forecasting</a>
<br>

### Exploring the data
<span>
    <img src="/assets/img/exploring0.PNG" style="width:80%">
</span>


<span>
    <img src="/assets/img/exploring.PNG" style="width:80%">
</span>



### Checking the amounts of zeros in dataframe
```
plt.rcParams['figure.figsize']=(10,10)
sns.heatmap(X_train_data == 0, yticklabels = False, cbar=False, cmap = 'viridis')
```

<span>
    <img src="/assets/img/zeros.PNG" style="width:50%">
</span>

### Data cleaning 

```
X_train_data['date'] = pd.to_datetime(X_train_data['date'], format='%d%b%Y')


#merging the sales values with the features dataframe
variables = list(X_train_data.columns.values)
variables = variables[2:]
products = X_train_data.key.unique()

X_train_data[variables] = X_train_data[variables].apply(pd.to_numeric)
X_train_data["sales"] = ""


for row in y_train_data.itertuples():
    X_train_data.loc[((X_train_data['key']==row.key) & (X_train_data['date'] == row.date)), 'sales'] = row.y
    
    
#Checking the merg
display(X_train_data.head())
```


### Analyzing the sales data

```
X_train_plot = X_train_data.copy()
uniqueValues = X_train_plot['key'].unique()

fig = go.Figure()
for unique in uniqueValues:
    temp_df_plot = X_train_plot[(X_train_plot['key'] == unique)]
    
    fig.add_trace(go.Scatter(x=temp_df_plot['date'],
                             y=temp_df_plot['sales'],
                             mode='lines+markers',
                             name=str(unique),
                             visible="legendonly"))
fig.update_layout(title="Try Clicking on the Legend Items!")

fig.show()  
```

<span>
    <img src="/assets/img/analyze.PNG" style="width:90%">
</span>


#### Comparing data from 2016 to one from 2017
```
start_date_train = np.datetime64('2016-01-01')
end_date_train = np.datetime64('2016-12-31')
start_date_test = np.datetime64('2017-01-01')
end_date_test = np.datetime64('2017-12-31')
 
train = X_train_data.loc[(X_train_data['date'] > start_date_train) & (X_train_data['date'] <= end_date_train)]
test = X_train_data.loc[(X_train_data['date'] > start_date_test) & (X_train_data['date'] <= end_date_test)]

train = train.fillna(train.mean())
test = test.fillna(test.mean())

uniqueValues = test['key'].unique()
fig2 = go.Figure()
for unique in uniqueValues:
    temp_train = train[(train['key'] == unique)]    
    temp_test = test[(test['key'] == unique)]
    fig2.add_trace(go.Scatter(x=temp_test['date'].dt.month,
                             y=temp_test['sales'],
                             mode='markers+lines',
                             name='actual 2017:' + str(unique),
                             visible="legendonly",
                             legendgroup=str(unique)))
    fig2.add_trace(go.Scatter(x=temp_train['date'].dt.month,
                             y=temp_train['sales'],
                             mode='markers+lines',
                             name='naive prediction- 2016:' + str(unique),
                             visible="legendonly",
                             legendgroup=str(unique)))
    
fig2.update_layout(title="Try Clicking on the Legend Items!")
fig2.show()
```
<span>
    <img src="/assets/img/naive.png" style="width:90%">
</span>


#### Compensating missing data
```
from impyute.imputation.cs import mice
imputed = mice(X.values)
df = pd.DataFrame(imputed, index = X.index, columns = X.columns)
X_train_mice = df.copy()
X_train_mice = X_train_mice.convert_dtypes(convert_floating=True)
```

#### One-hot encoding

```
encoded = pd.get_dummies(X_train_mice.key)
X_train_mice = pd.concat([X_train_mice, encoded], axis=1)
X_train_mice.shape
del X_train_mice['key']
```

#### Data split - train-test data

```
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=21)
y_train = y_train.astype(np.float64)
X_train = X_train.astype(np.float64)
X_test = X_test.astype(np.float64)
y_test = y_test.astype(np.float64)
```



### Gradient Boosting Regressor
```
from sklearn.ensemble import GradientBoostingRegressor
from sklearn.metrics import mean_squared_error

regressor = GradientBoostingRegressor(max_depth=2, n_estimators=3, learning_rate=1.0)
regressor.fit(X_train, y_train)
errors = [mean_squared_error(y_test, y_pred) for y_pred in regressor.staged_predict(X_test)]
best_n_estimators = np.argmin(errors)
best_regressor = GradientBoostingRegressor(max_depth=7, n_estimators=best_n_estimators, learning_rate=1.0)
best_regressor.fit(X_train, y_train)
y_pred_gbr = best_regressor.predict(X_test)

mean = np.mean(y_pred_gbr)
for sales in y_pred_gbr:
    if sales < 0:
        sales = mean
    elif sales > 15000:
        sales = mean
        
print('WMAPE: ', calculate_mean_absolute_percentage_error_2(y_test, y_pred_gbr).values)
wmape.append(calculate_mean_absolute_percentage_error_2(y_test, y_pred_gbr).values)

show_model_performance(y_pred_gbr)
```
<span>
    <img src="/assets/img/gbr1.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/gbr2.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/gbr3.png" style="width:33%">
</span>



### Extreme Gradient Boosting Regressor
```
import xgboost as xgb
train_dmatrix = xgb.DMatrix(data = X_train, label = y_train)
test_dmatrix = xgb.DMatrix(data = X_test, label = y_test)
  
Parameter dictionary specifying base learner
param = {"booster":"gblinear", "objective":"reg:squarederror"}
  
xgb = xgb.XGBRegressor(n_estimators=100, learning_rate=0.09, gamma=0, subsample=0.70,colsample_bytree=1, max_depth=50)
xgb.fit(X_train, y_train)
y_pred_XGB = xgb.predict(X_test)

mean = np.mean(y_pred_XGB)
for sales in y_pred_XGB:
    if sales < 0:
        sales = mean
    elif sales > 15000:
        sales = mean

print('WMAPE: ', calculate_mean_absolute_percentage_error_2(y_test, y_pred_XGB).values)
wmape.append(calculate_mean_absolute_percentage_error_2(y_test, y_pred_XGB).values)

show_model_performance(y_pred_XGB)
```
<span>
    <img src="/assets/img/xgb1.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/xgb2.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/xgb3.png" style="width:33%">
</span>


### Random Forest Regressor
```
from sklearn.ensemble import RandomForestRegressor
regressor = RandomForestRegressor(n_estimators = 1000, random_state = 42)
regressor.fit(X_train, y_train)
rdforrest_pred = regressor.predict(X_test)

mean = np.mean(rdforrest_pred)
for sales in rdforrest_pred:
    if sales < 0:
        sales = mean

print('WMAPE: ', calculate_mean_absolute_percentage_error_2(y_test, rdforrest_pred).values)
wmape.append(calculate_mean_absolute_percentage_error_2(y_test, rdforrest_pred).values)

show_model_performance(rdforrest_pred)
```
<span>
    <img src="/assets/img/rf1.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/rf2.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/rf3.png" style="width:33%">
</span>

### Summary

```
fig3 = go.Figure(data=[go.Table(
    header=dict(values=list([str('Model'),str('Weighted Mean Absolute Percentage Error')]),
                fill_color='darkslategray',
                align='center',
                font=dict(color='white', size=12)),
    cells=dict(values=[['Naive Forecast','Gradient Boosting Regressor','XGBoost','Random Forest Regressor'],
                       [wmape[0], wmape[1], wmape[2],wmape[3]]],
               line_color='darkslategray',
               fill_color = 'lightgrey',
               align='center'))
                      ])

fig3.show()
```
<span>
    <img src="/assets/img/forecasting_summary.png" style="width:100%">
</span>

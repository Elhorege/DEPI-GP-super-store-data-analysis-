# Sales Data Analysis Report

This report presents a comprehensive analysis of the SuperStore dataset, which includes steps for data preparation, exploration, and predictive modeling. The focus is on cleaning the data, identifying trends, and building a predictive model to support data-driven decision-making.

## Data Preparation and Cleanup

To ensure the dataset was ready for analysis, several steps were taken to clean and prepare the data. These include handling returns, ensuring profitability data consistency, and converting date columns to the proper format. Below is a detailed description of each step:

### 1. Handling Returns
Returns were identified by negative values in the profit column. A new column, **Returns**, was created to flag these values. Any negative profit was treated as a return and stored in this new column, while the original profit column was adjusted to remove the negative values.

```python
df['Return'] = df['profit'].apply(lambda x: x if x < 0 else 0)
df['profit'] = df['profit'].apply(lambda x: 0 if x < 0 else x)
```
If the profit was negative, it was placed into the Returns column, and the original profit column was reset to 0.
This ensures that the profit column only contains positive values, while Returns captures the losses.

### 2.Date Formatting
The order_date and ship_date columns were initially in string format with inconsistent separators ('/'). These were replaced with hyphens ('-') for consistency, and the strings were then converted into the proper datetime format using the pd.to_datetime() function.

```python
df['ship_date'] = df['ship_date'].str.replace('/', '-', regex=False)
df['order_date'] = df['order_date'].str.replace('/', '-', regex=False)
df['order_date'] = pd.to_datetime(df['order_date'], format='%d-%m-%Y')
df['ship_date'] = pd.to_datetime(df['ship_date'], format='%d-%m-%Y')
```
This step ensures that the dates are uniform and correctly parsed into Python's datetime object for easier analysis and manipulation later on.

### 3.Saving the Cleaned Data
After these transformations, the cleaned dataset was saved into a new CSV file, ensuring the updated data was preserved for further analysis.

```python
df.to_csv('Super_Store.csv', index=False)
```
The dataset was saved without row indices to maintain a clean format suitable for external sharing or further processing.

## Exploratory Data Analysis (EDA)

This EDA provides an analysis of sales data using Python libraries such as NumPy, Pandas, and Plotly for data processing and visualization. The objective of this analysis is to explore various dimensions of sales performance, including customer distribution by state, order frequency by shipping mode and segment, and overall sales and profit by region.

## Libraries Used
The following libraries were imported for data processing and visualization:

- NumPy: For numerical operations and handling arrays.
- Pandas: For data manipulation and analysis, particularly for reading CSV files.
- Plotly Express: For interactive data visualization.
- Seaborn: For enhanced statistical data visualization.
- Matplotlib: For creating static, animated, and interactive visualizations in Python.
## Creating a Custom Color Palette
To improve the aesthetic appeal of visualizations, a custom color palette was created. This palette consists of four distinct colors, which were displayed using Seaborn and Matplotlib.

```python
# Creating a custom palette
colors = ['#1192AA','#00B8C0', '#73D4D3', '#90F2F0']

# Visualizing the custom palette
sns.palplot(colors)
plt.gcf().set_size_inches(8, 2.5)

# Adding color labels
for idx, values in enumerate(colors):
    plt.text(idx - 0.375, 0, colors[idx], {'font': "Courier New", 'size': 16, 'weight': 'bold', 'color': 'black'}, alpha=0.7)
plt.gcf().set_facecolor('white')

# Displaying the palette
plt.show() 
```
![alt text](<New folder/output.png>)

## State Abbreviation Dictionary
A dictionary mapping U.S. state names to their corresponding abbreviations was created to facilitate data processing tasks that require state identification. This dictionary includes all U.S. states and territories.

```python
us_state_to_abbrev = {
    "Alabama": "AL",
    "Alaska": "AK",
    "Arizona": "AZ",
    "Arkansas": "AR",
    "California": "CA",
    "Colorado": "CO",
    "Connecticut": "CT",
    "Delaware": "DE",
    "Florida": "FL",
    "Georgia": "GA",
    "Hawaii": "HI",
    "Idaho": "ID",
    "Illinois": "IL",
    "Indiana": "IN",
    "Iowa": "IA",
    "Kansas": "KS",
    "Kentucky": "KY",
    "Louisiana": "LA",
    "Maine": "ME",
    "Maryland": "MD",
    "Massachusetts": "MA",
    "Michigan": "MI",
    "Minnesota": "MN",
    "Mississippi": "MS",
    "Missouri": "MO",
}
```
### 1.Customer Distribution by State
```python

import plotly.graph_objects as go

# Create new column with State Abbreviations
data['State_abb'] = data['state'].replace(us_state_to_abbrev)

# Plot
fig = go.Figure(data=go.Choropleth(
    locations=data['State_abb'].value_counts().index,  # Spatial coordinates
    z=data['State_abb'].value_counts(),                # Data to be color-coded
    locationmode='USA-states',
    colorscale='teal', zmin=1, zmax=1000
))

fig.update_layout(
    font=dict(size=14),
    title={
        'text': "Number of Customers by State Map",
        'y': 0.95,
        'x': 0.5
    },
    geo_scope='usa',  # limit map scope to USA
)

fig.show()
```
## Insight
The choropleth map illustrates the distribution of customers across the United States. States with higher concentrations of customers are visually represented, helping identify geographical trends. This information can assist in targeting marketing efforts and understanding regional preferences.

![alt text](<New folder/1.png>)

### 2.Frequency of Orders by Shipping Modes
```python

import plotly.express as px

fig = px.bar(x=data['ship_mode'].value_counts().index, 
             y=data['ship_mode'].value_counts(),
             color=data['ship_mode'].value_counts().index,
             template='simple_white', 
             color_discrete_sequence=colors)

# Set Title and x/y axis labels
fig.update_layout(
    xaxis_title="Shipping Mode",
    yaxis_title="Number of Orders",
    showlegend=False,
    font=dict(size=14),
    title={
        'text': "Frequency of Orders by Shipping Modes",
        'y': 0.95,
        'x': 0.5
    }
)

fig.show()
```
## Insight
The bar chart displays the frequency of orders based on different shipping modes. Analyzing this data allows businesses to determine the most popular shipping options among customers, which can inform logistics and shipping strategy decisions.

![alt text](<New folder/2.png>)

### 3.Frequency of Orders by Segment
```python
Copy code
fig = px.bar(x=data['segment'].value_counts().index, 
             y=data['segment'].value_counts(),
             color=data['segment'].value_counts().index,
             template='simple_white', 
             color_discrete_sequence=colors)

# Set Title and x/y axis labels
fig.update_layout(
    xaxis_title="Segment",
    yaxis_title="Number of Orders",
    showlegend=False,
    font=dict(size=14),
    title={
        'text': "Frequency of Orders by Segment",
        'y': 0.95,
        'x': 0.5
    }
)

fig.show()
```
## Insight
This bar chart represents the frequency of orders across different market segments. Understanding segment performance can provide insights into customer demographics and preferences, guiding product offerings and marketing strategies.

![alt text](<New folder/3.png>)

### 2.Frequency of Orders by Region
```python
import plotly.express as px

fig = px.bar(x=data['region'].value_counts().index, 
             y=data['region'].value_counts(),
             color=data['region'].value_counts().index,
             template='simple_white', 
             color_discrete_sequence=colors)

# Set Title and x/y axis labels
fig.update_layout(
    xaxis_title="Region",
    yaxis_title="Number of Orders",
    showlegend=False,
    font=dict(size=14),    
    title={
        'text': "Frequency of Orders by Region",
        'y': 0.95,
        'x': 0.5
    }
)

fig.show()
```
## Insight
The bar chart displays the number of orders received from different regions. Identifying regions with the highest order volumes can inform targeted marketing strategies and resource allocation.

![alt text](<New folder/4.png>)

### 3.Frequency of Orders by Category
```python
Copy code
fig = px.bar(x=data['category'].value_counts().index, 
             y=data['category'].value_counts(),
             color=data['category'].value_counts().index,
             template='simple_white', 
             color_discrete_sequence=colors)

# Set Title and x/y axis labels
fig.update_layout(
    xaxis_title="Category",
    yaxis_title="Number of Orders",
    showlegend=False,
    font=dict(size=14),    
    title={
        'text': "Frequency of Orders by Category",
        'y': 0.95,
        'x': 0.5
    }
)

fig.show()
```
## Insight
This visualization shows the frequency of orders for each product category. Understanding category performance helps identify popular products, guiding inventory management and marketing efforts.

![alt text](<New folder/5.png>)

### 4.Frequency of Orders by Sub-Category
```python
Copy code
fig = px.bar(x=data['sub_category'].value_counts().index, 
             y=data['sub_category'].value_counts(),
             template='simple_white', 
             color_discrete_sequence=colors)

# Set Title and x/y axis labels
fig.update_layout(
    xaxis_title="Sub-Category",
    yaxis_title="Number of Orders",
    font=dict(size=14),    
    title={
        'text': "Frequency of Orders by Sub-Category",
        'y': 0.95,
        'x': 0.5
    }
)

fig.show()
```
## Insight
The bar chart illustrates the order frequency by sub-category, providing a more granular view of product performance. This can help in identifying niche markets and optimizing product offerings.

![alt text](<New folder/6.png>)

### 5.Total Sales and Profit Analysis by State
Total Sales by State
```python
# Make a copy of original table
data_copy = data.copy()

# Group by State_abb and aggregate sum function on Sales and Profit columns
data_sum_by_states = data_copy[['State_abb', 'sales', 'profit']].groupby('State_abb').sum()

# Reset index
data_sum_by_states.reset_index(inplace=True)

# Plot
fig = go.Figure(data=go.Choropleth(
    locations=data_sum_by_states['State_abb'],  # Spatial coordinates
    z=data_sum_by_states['sales'],               # Data to be color-coded
    locationmode='USA-states',
    colorscale='teal',
))

fig.update_layout(
    font=dict(size=14),    
    title={
        'text': "Total Sales by State Map",
        'y': 0.95,
        'x': 0.5
    },
    geo_scope='usa',  # limit map scope to USA
)

fig.show()
```
## Insight
The choropleth map displays total sales by state. States with higher sales volumes can indicate potential markets for expansion and targeted promotions.

![alt text](<New folder/7.png>)

### 6.Total Profit by State
```python
fig = go.Figure(data=go.Choropleth(
    locations=data_sum_by_states['State_abb'],  # Spatial coordinates
    z=data_sum_by_states['profit'],              # Data to be color-coded
    locationmode='USA-states',
    colorscale='tropic_r', zmin=-30000, zmax=30000
))

fig.update_layout(
    font=dict(size=14),    
    title={
        'text': "Total Profit by State Map",
        'y': 0.95,
        'x': 0.5
    },
    geo_scope='usa',  # limit map scope to USA
)

fig.show()
```
## Insight
This map shows total profit by state. Analyzing profit distribution helps identify profitable markets and areas that may require strategic improvements.

![alt text](<New folder/8.png>)

### 7. Number of Orders by Shipping Mode Map
```python
# Create a new data table for the chart
data1 = data.copy()

# Split Ship Mode values in unique columns
for ship_mode in data1['ship_mode'].unique():
    data1[ship_mode] = data1.loc[data1['ship_mode'] == ship_mode]['ship_mode']

# Group by State_abb and count
data1 = data1[['State_abb', 'Second Class', 'Standard Class', 'First Class', 'Same Day']].groupby('State_abb').count()
data1.reset_index(inplace=True)

# Create the figure
fig = go.Figure()
colors = ["crimson", "lightseagreen", "royalblue", "orange", "lightgrey"]
columns = ["Second Class", "Standard Class", "First Class", "Same Day"]

# Add traces for each shipping mode
for i, column in enumerate(columns):
    fig.add_trace(go.Scattergeo(
         locations=data1['State_abb'],
         locationmode='USA-states',
         marker=dict(size=data1[column], color=colors[i], sizemode='area'),
         name=column
    ))

# Update layout
fig.update_layout(
    font=dict(size=14),
    title={'text': "Number of Orders by Shipping Mode Map<br>(Click legend to toggle traces)", 'y': 0.95, 'x': 0.5},
    geo=dict(scope='usa', landcolor='rgb(217, 217, 217)')
)
fig.show()
```
## Insights
- Shipping Mode Distribution: Shows regional preferences for different shipping modes.
- Same-Day Deliveries: Highlights areas with high demand for urgent shipping.
- Standard Class Usage: Indicates cost-effective shipping preferences in certain states.

![alt text](<New folder/9.png>)

### 8.Number of Orders by Segment Map
```python
# Create new data table for the segment chart
data1 = data.copy()

# Split Segment values in unique columns
for segment in data1['segment'].unique():
    data1[segment] = data1.loc[data1['segment'] == segment]['segment']

# Group by State_abb and count
data1 = data1[['State_abb', 'Consumer', 'Corporate', 'Home Office']].groupby('State_abb').count()
data1.reset_index(inplace=True)

# Create the figure
fig = go.Figure()
colors = ["crimson", "lightseagreen", "royalblue", "orange", "lightgrey"]
columns = ['Consumer', 'Corporate', 'Home Office']

# Add traces for each segment
for i, column in enumerate(columns):
    fig.add_trace(go.Scattergeo(
         locations=data1['State_abb'],
         locationmode='USA-states',
         marker=dict(size=data1[column], color=colors[i], sizemode='area'),
         name=column
    ))

# Update layout
fig.update_layout(
    font=dict(size=14),
    title={'text': "Number of Orders by Segment Map<br>(Click legend to toggle traces)", 'y': 0.95, 'x': 0.5},
    geo=dict(scope='usa', landcolor='rgb(217, 217, 217)')
)
fig.show()
```
## Insights
- Segment Distribution: Reveals order distribution among Consumer, Corporate, and Home Office segments.
- Corporate Orders: Identifies states with strong business relationships.
- Home Office Trends: Reflects evolving needs due to remote work.

![alt text](<New folder/10.png>)

### 9. Number of Orders by Category Map
``` python
# Create new data table for the category chart
data1 = data.copy()

# Split Category values in unique columns
for category in data1['category'].unique():
    data1[category] = data1.loc[data1['category'] == category]['category']

# Group by State_abb and count
data1 = data1[['State_abb', 'Furniture', 'Office Supplies', 'Technology']].groupby('State_abb').count()
data1.reset_index(inplace=True)

# Create the figure
fig = go.Figure()
colors = ["crimson", "lightseagreen", "royalblue", "orange", "lightgrey"]
columns = ['Furniture', 'Office Supplies', 'Technology']

# Add traces for each category
for i, column in enumerate(columns):
    fig.add_trace(go.Scattergeo(
         locations=data1['State_abb'],
         locationmode='USA-states',
         marker=dict(size=data1[column] * 2, color=colors[i], sizemode='area'),
         name=column
    ))

# Update layout
fig.update_layout(
    font=dict(size=14),
    title={'text': "Number of Orders by Category Map<br>(Click legend to toggle traces)", 'y': 0.95, 'x': 0.5},
    geo=dict(scope='usa', landcolor='rgb(217, 217, 217)')
)
fig.show()
```
## Insights
- Category Performance: Shows popularity of Furniture, Office Supplies, and Technology categories by state.
- Technology Demand: Indicates regions with high demand for electronics.
- Office Supplies Trends: Correlates with business hubs or educational institutions.

![alt text](<New folder/11.png>)

### 10. Number of Orders by Sub-Category Map
```python
# Create new data table for the sub-category chart
data1 = data.copy()

# Split Sub-Category values in unique columns
for category in data1['sub_category'].unique():
    data1[category] = data1.loc[data1['sub_category'] == category]['sub_category']

# Group by State_abb and count
data1 = data1[['State_abb', 'Bookcases', 'Chairs', 'Labels', 'Tables', 'Storage', 'Furnishings', 'Art', 'Phones', 'Binders', 'Appliances', 'Paper', 'Accessories', 'Envelopes', 'Fasteners', 'Supplies', 'Machines', 'Copiers']].groupby('State_abb').count()
data1.reset_index(inplace=True)

# Create the figure
fig = go.Figure()
columns = ['Bookcases', 'Chairs', 'Labels', 'Tables', 'Storage', 'Furnishings', 'Art', 'Phones', 'Binders', 'Appliances', 'Paper', 'Accessories', 'Envelopes', 'Fasteners', 'Supplies', 'Machines', 'Copiers']

# Add traces for each sub-category
for i, column in enumerate(columns):
    fig.add_trace(go.Scattergeo(
         locations=data1['State_abb'],
         locationmode='USA-states',
         marker=dict(size=data1[column] * 5, sizemode='area'),
         name=column
    ))

# Update layout
fig.update_layout(
    font=dict(size=14),
    title={'text': "Number of Orders by Sub-Category Map<br>(Click legend to toggle traces)", 'y': 0.95, 'x': 0.5},
    geo=dict(scope='usa', landcolor='rgb(217, 217, 217)')
)
fig.show()
```
## Insights
- Sub-Category Variability: Details preferences for specific products like Chairs and Storage.
- Key Products: Identifies top-selling items driving sales.
- Niche Marketing Opportunities: Guides targeted campaigns based on regional preferences.

![alt text](<New folder/12.png>)


## Model Evaluation Conclusion

The model was optimized with the following best parameters:
- **learning_rate** = 0.5
- **max_depth** = 7
- **n_estimators** = 200

## Performance Metrics
- **Root Mean Squared Error (RMSE)**: 0.00177
- **R² Score**: 0.9611
- **Mean of y_train**: 0.14296

### Interpretation:
- The **RMSE** of 0.00177 is extremely low, suggesting that the model's predictions are highly accurate, with minimal deviation from the actual values of the "discount" variable.
- The **R² Score** of 0.9611 means that the model explains **96.1%** of the variance in the "discount" values. This indicates that the model captures almost all of the underlying patterns in the data.
- The mean value of `y_train` is 0.14296, which further emphasizes how small the error (RMSE) is in comparison to the target variable's scale. The average error is much smaller than the mean of the "discount," suggesting a strong predictive performance.

#### Model Conclusion:
The model performs exceptionally well, providing highly accurate predictions of the "discount" based on the data. With a high R² score and a very low RMSE, the model is both effective in explaining the variance and minimizing the prediction error. This suggests that the model is well-suited for predicting the "discount" variable with minimal room for improvement.


Here’s a more concise conclusion that focuses on the discount prediction:

# Conclusion
The analysis of the SuperStore dataset provides valuable insights into sales patterns and customer behavior. By effectively preparing the data and exploring key trends, we identified significant factors influencing sales performance.

The predictive model developed in this analysis successfully forecasts potential discounts, allowing the business to optimize pricing strategies and enhance customer satisfaction. These insights are crucial for informing marketing efforts and improving sales outcomes.

In summary, the findings offer actionable recommendations for stakeholders to drive strategic initiatives and strengthen the company’s competitive position in the retail market.
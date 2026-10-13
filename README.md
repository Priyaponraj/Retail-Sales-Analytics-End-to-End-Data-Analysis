
# Retail Sales Analytics — End-to-End Data Analysis

## 📌 Project Summary

This is a portfolio project demonstrating a complete data analysis workflow on a retail sales dataset of 7,500 transactions across Tamil Nadu, India. The project covers everything from raw data ingestion to interactive dashboards using Python, Pandas, and Plotly.

## Focus Areas:

* Data cleaning and profiling

* Feature engineering (temporal + geographic)

* Business KPI measurement

* Interactive visual storytelling


## 📁 Dataset Overview

Source: retail_sales_dataset_7500 (1).csv

[retail_sales_dataset_7500 (1).csv](https://github.com/user-attachments/assets/84f69ee6-16ec-4958-8362-3e826db2aaa8)


[Snapshot](https://github.com/user-attachments/assets/53802c57-640e-4a48-a39e-7535a3e3c3d1)


[🛠️ Tools & Libraries](https://github.com/user-attachments/assets/438bc762-82b8-4cb5-9f75-3d33a47d8ecd)

## 🔄 Analysis Workflow

```
Data Loading
    ↓
Automated EDA (Profiling Report)
    ↓
Schema Inspection
    ↓
Feature Engineering (Date + Geo)
    ↓
KPI Measurement
    ↓
Exploratory Analysis
    ↓
Interactive Visualization
    ↓
Export Cleaned Dataset

```

### Step 1 — Data Loading
Loaded CSV into a Pandas DataFrame in Google Colab.

### Step 2 — Automated EDA
Generated an HTML profiling report covering schema, distributions, and missing values.

### Step 3 — Feature Engineering

```
# Convert to datetime
df['order_date'] = pd.to_datetime(df['order_date'])

# Extract temporal features
df['month_name'] = df['order_date'].dt.month_name()
df['month_num']  = df['order_date'].dt.month
df['quarter']    = df['order_date'].dt.quarter
df['year']       = df['order_date'].dt.year

# Add geographic coordinates
df['latitude']  = df['city'].map(lambda x: city_coordinates[x][0])
df['longitude'] = df['city'].map(lambda x: city_coordinates[x][1])
```


## 📊 Analysis & Findings

### KPI Measurement

```
print("="*50)
print("           RETAIL DATASET SUMMARY")
print("="*50)

print(f"Total Orders           : {df['order_id'].nunique()}")
print(f"Total Unique Customers : {df['customer_name'].nunique()}")
print(f"Total Categories       : {df['category'].nunique()}")
print(f"Total Products         : {df['product'].nunique()}")
print(f"Total Cities           : {df['city'].nunique()}")
print(f"Total Regions          : {df['region'].nunique()}")
print(f"Total Sales            : ₹{df['sales'].sum():,.2f}")
print(f"Total Profit           : ₹{df['profit'].sum():,.2f}")
print(f"Avg Discount %         : {df['discount_percent'].mean():.2f}%")
print(f"Date Range             : {df['order_date'].min()} to {df['order_date'].max()}")
print("="*50)
```

### Region-wise Sales Analysis
Calculate the total sales generated in each region to identify top-performing and underperforming regions.

```
region_wise_sales=df.groupby('region')['sales'].sum()

print('Region wise sales: ', region_wise_sales)
```

### Top 5 Products by Sales
Identify the top 5 best-selling products by total revenue to understand which products drive the most sales.

```
# Top 5 Product

df['product'].unique()

print('Top 5 product')

top_5_product=df.groupby('product')['sales'].sum().nlargest(5)
```

### City-wise Sales Analysis
Calculate the total sales for each city and rank them to identify the top-performing and weakest cities.

```
# city wise sales

city_by_sales = df.groupby('city')['sales'].sum().sort_values(ascending=False)

print('city by sales : ',city_by_sales)
```

### High & Low Sales Months Analysis
Identify the top 5 highest-performing months and bottom 5 lowest-performing months by total sales to understand seasonality and plan inventory/marketing accordingly.

```
# Top 5 high sales months
high_sales_month = (
    df.groupby('month_name')['sales'].sum().sort_values(ascending=False).head(5)
)
print('\n High Sales Months:\n ', high_sales_month)

# Top 5 low sales months
low_sales_month = (
    df.groupby('month_name')['sales'].sum().sort_values(ascending=True).head(5)
)
print('\n Low Sales Months: \n', low_sales_month)
```

### Product-wise Summary (Multi-Metric Analysis)
Build a consolidated product-level summary that shows units sold, total sales, total profit, and number of unique orders for each product — then rank products by units sold.

```
product_summary = (
    df.groupby('product').agg(
                             unit_sold=('quantity', 'sum'),
                             total_sales=('sales', 'sum'),
                             total_profit=('profit', 'sum'),
                             orders=('order_id', 'nunique')
      )
      .sort_values('unit_sold', ascending=False)
)

print("Product-wise Summary (Top 10):")
print(product_summary.head(10))

```

### How many orders used each payment mode.

Business Insight

Shows which payment mode is most popular by order count.

UPI leading → digital-first customer base.

```
payment_count = (
    df.groupby('payment_mode', as_index=False)['order_id']
      .count()
      .rename(columns={'order_id': 'orders'})
      .sort_values('orders', ascending=False)
)
print(payment_count)

```

### Data visualization
Installing Plotly

Install Plotly, an interactive plotting library, to create dynamic charts (hover tooltips, zoom, pan) for the retail sales analysis.

#### Command : pip install plotly

```
import plotly.express as px
```

### Month-wise Sales
```
sales_month = (
    df.groupby(['month_num', 'month_name'], as_index=False)['sales']
      .sum()
      .sort_values('month_num')
)

fig = px.line(
              sales_month,
              x='month_name',
              y='sales',
              title='Monthly Sales',
              labels={'month_name': 'Month', 'sales': 'Total Sales'},
              color_discrete_sequence=['#5d9854']
)
fig.show()
```
### Top 5 Products by Quantity

Identify and visualize the top 5 products based on total units sold, using an interactive Plotly bar chart.

```
import plotly.express as px

top5 = (
    df.groupby('product', as_index=False)['quantity']
      .sum()
      .sort_values('quantity', ascending=False)
      .head(5)
)

fig = px.bar(
    top5,
    y='product',
    x='quantity',
    title='Top 5 Products by Quantity',
    orientation='h',
    labels={
        'product': 'Product Name',
        'quantity': 'Total Quantity'
    },
    color_discrete_sequence=['blue']
)

fig.update_layout(
    plot_bgcolor='lightgray',
    paper_bgcolor='white'

)

fig.show()
```

### Sales by Category
Visualize the share of total sales contributed by each product category using an interactive Plotly pie chart.

```
# Sales by Category

fig=px.pie(df,values='sales',names='category',title='sales by category')

fig.show()
```

### Sales Distribution Analysis
```
# Sales distribution
fig = px.histogram(
    df,
    x='sales',
    title='Sales Distribution',
    nbins=50
)
fig.show()
```

### Outlier Analysis — Sales by Category
Identify unusual sales values (outliers) within each product category using a box plot.

```
# Outlier analysis

fig=px.box(df,x='category',y='sales',title='Outlier analysis')

fig.show()
```

### Sales Hierarchy Treemap (Category → Product)

Visualize how total sales are distributed across a two-level hierarchy: Category (parent) → Product (child), using nested rectangles.

```
fig=px.treemap(df,path=['category','product'],values='sales',title='sales hierarchy')

fig.show()
```

## Adds latitude and longitude to each row based on the city — enabling geographic visualization (maps, scatter maps, etc.).

```
city_coordinates = {
    'Chennai': (13.0827, 80.2707),
    'Madurai': (9.9252, 78.1198),
    'Salem': (11.6643, 78.1460),
    'Trichy': (10.7905, 78.7047),
    'Coimbatore': (11.0168, 76.9558),
    'Tirunelveli': (8.7139, 77.7567)
}

df['latitude'] = df['city'].map(lambda x: city_coordinates[x][0])
df['longitude'] = df['city'].map(lambda x: city_coordinates[x][1])
```

## City-wise Sales — Geographic Bubble Map

Visualize total sales across Tamil Nadu cities on an interactive map, with bubble size and color representing sales volume.

```
city_sales=(df.groupby(['city','latitude','longitude'],as_index=False)['sales'].sum())


fig=px.scatter_map(city_sales,
                   lat='latitude',
                   lon='longitude',
                   size='sales',
                   color='sales',
                   hover_name='city',
                   title='city wise sales',
                   zoom=5)

fig.show()
```

### Sales Distribution by Category& Product

```
category_sales = (
    df.groupby(['category', 'product'], as_index=False)['sales']
      .sum()
)

fig = px.sunburst(
    category_sales,
    path=['category', 'product'],
    values='sales',
    title='Sales Distribution by Category& Product'
)

fig.show()
```


## 🎓 Skills Demonstrated

✅ Data cleaning with Pandas

✅ Datetime handling and feature engineering

✅ GroupBy aggregations and multi-metric analysis

✅ Interactive visualization with Plotly

✅ Business KPI design (revenue, margin, AOV)

✅ Storytelling with data

✅ Automated EDA reporting



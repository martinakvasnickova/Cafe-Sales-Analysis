# Exploratory Data Analysis (EDA) on Café Sales Data

## Introduction

### Objective of the Analysis
The goal of this EDA is to identify key trends and patterns in café sales. We analyze factors influencing revenue, such as product popularity, seasonal trends, and variations across locations and payment methods. The insights will help improve operational efficiency and maximize sales.

### Context
The café operates in a competitive environment where consumer behavior is affected by seasonal demand, daily cycles, and customer preferences. Understanding these factors is crucial for optimizing product offerings and business strategies.

### Data Overview
The dataset contains 10,000 transaction records with the following attributes:
- **Transaction ID**: Unique identifier for each transaction.
- **Item**: Product purchased (e.g., Coffee, Cake, Smoothie).
- **Quantity**: Number of units sold.
- **Price Per Unit**: Unit price of the item.
- **Total Spent**: Total transaction amount.
- **Payment Method**: Type of payment (e.g., Credit Card, Cash, Digital Wallet).
- **Location**: Purchase location (In-store or Takeaway).
- **Transaction Date**: Date of purchase.

The dataset includes missing values and inconsistencies (e.g., "ERROR" or "UNKNOWN") that must be cleaned before further analysis.

---

## Data Preprocessing and Exploration

### Loading the Dataset
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv('../dirty_cafe_sales.csv')
```

### First Look at the Data
```python
df.head(10)
df.shape
df.isnull().sum()
df.dtypes
```

### Data Cleaning
```python
df['Quantity'] = pd.to_numeric(df['Quantity'], errors='coerce')
df['Price Per Unit'] = pd.to_numeric(df['Price Per Unit'], errors='coerce')
df['Total Spent'] = pd.to_numeric(df['Total Spent'], errors='coerce')
df['Transaction Date'] = pd.to_datetime(df['Transaction Date'], errors='coerce')
df = df.dropna(subset=['Item', 'Price Per Unit', 'Quantity', 'Total Spent'])
df.drop_duplicates(inplace=True)
```

### Handling Invalid Values
```python
df['Total Spent'] = df['Quantity'] * df['Price Per Unit']
```

---

## Descriptive Statistics

### Revenue Analysis
```python
total_revenue = df['Total Spent'].sum()
daily_revenue = df.groupby('Transaction Date')['Total Spent'].sum()
daily_revenue_avg = daily_revenue.mean()
revenue_median = daily_revenue.median()
print(f'Total Revenue: ${total_revenue}')
print(f'Average Daily Revenue: ${daily_revenue_avg}')
print(f'Median Daily Revenue: ${revenue_median}')
```

### Revenue Variability
```python
revenue_variance = daily_revenue.var()
revenue_std_dev = daily_revenue.std()
print(f'Variance: {revenue_variance}')
print(f'Standard Deviation: {revenue_std_dev}')
```

### Revenue by Product Category
```python
revenue_by_item = df.groupby('Item')['Total Spent'].sum().sort_values(ascending=False)
revenue_by_item.plot(kind='barh', color='skyblue')
plt.title('Revenue by Product Categories')
plt.xlabel('Revenue')
plt.show()
```

---

## Time Series Analysis

### Sales Trends by Day of the Week
```python
df['Weekday'] = df['Transaction Date'].dt.day_name()
weekday_revenue = df.groupby('Weekday')['Total Spent'].mean()
weekday_revenue.plot(kind='line', marker='o', color='green')
plt.title('Average Sales by Day of the Week')
plt.ylabel('Average Sales')
plt.show()
```

### Sales Trends by Month
```python
df['Month'] = df['Transaction Date'].dt.month_name()
monthly_revenue = df.groupby('Month')['Total Spent'].sum()
monthly_revenue.plot(kind='bar', color='orange')
plt.title('Total Revenue by Month')
plt.ylabel('Revenue')
plt.xticks(rotation=45)
plt.show()
```

---

## Outlier Analysis

### Identifying Anomalies in Revenue
```python
q1 = df['Total Spent'].quantile(0.25)
q3 = df['Total Spent'].quantile(0.75)
iqr = q3 - q1
lower_bound = q1 - 1.5 * iqr
upper_bound = q3 + 1.5 * iqr
outliers = df[(df['Total Spent'] < lower_bound) | (df['Total Spent'] > upper_bound)]
print(f'Number of Outliers: {len(outliers)}')
```

### Visualization of Outliers
```python
sns.boxplot(y=df['Total Spent'])
plt.title('Identifying Anomalies in Sales')
plt.show()
```

---

## Key Findings and Recommendations

### Main Findings
- **Sales by Day of the Week**:
  - The highest sales occur on Tuesdays and Thursdays.
  - The lowest sales are observed on Mondays.
- **Top-Selling Products**:
  - Coffee contributes over 60% of total sales.
  - Juices, salads, cakes, and cookies have steady sales across all periods.
- **Seasonal Trends**:
  - No significant seasonal fluctuations were detected.
  - Some extreme sales peaks likely correspond to holidays or special events.
- **Potential Issues**:
  - Low sales on Mondays indicate a need for promotions or marketing strategies.
  - Data anomalies suggest possible operational issues requiring further investigation.

### Recommendations
- Implement promotions on Mondays to boost sales.
- Leverage high weekend demand with premium product offerings.
- Monitor and analyze unusual sales patterns to optimize operational decisions.



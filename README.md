# Big_data_Project
# E-Commerce Customer Behaviour Analysis Using Big Data Analytics

## Project Overview

This project analyzes e-commerce consumer purchasing behaviour using **Apache Spark (PySpark)** and Python. The objective is to understand customer purchasing patterns, identify behavioural customer segments, explore temporal ordering trends, and examine product reorder behaviour.

The project combines distributed data processing, data cleaning, exploratory data analysis (EDA), feature engineering, and unsupervised machine learning to derive actionable insights for customer segmentation and marketing strategy.

## Objectives

- Process and analyze large-scale e-commerce transaction data using PySpark.
- Clean and preprocess the dataset by examining missing values, duplicates, data types, and outliers.
- Perform feature engineering to derive customer-level purchasing metrics.
- Explore temporal purchasing patterns and product reorder behaviour.
- Segment customers using the K-Means clustering algorithm.
- Profile customer segments based on purchase frequency, basket size, reorder rate, and purchase intervals.
- Compare Spark and Pandas transformation-definition times.

## Dataset

**Source:** [E-Commerce Consumer Behaviour Dataset — Kaggle](https://www.kaggle.com/datasets/hunter0007/ecommerce-dataset-for-predictive-marketing-2023/data)

The dataset contains e-commerce transaction records with information about customers, orders, products, departments, and purchasing behaviour.

| Attribute | Description |
|---|---|
| `user_id` | Unique customer identifier |
| `order_id` | Unique order identifier |
| `order_number` | Sequence number of a customer's order |
| `order_dow` | Day of the week on which the order was placed |
| `order_hour_of_day` | Hour of the day when the order was placed |
| `days_since_prior_order` | Days since the customer's previous order |
| `product_id` | Product identifier |
| `add_to_cart_order` | Position of the product in the cart |
| `reordered` | Indicates whether the product was reordered |
| `department` | Product department |

### Dataset Summary

- Approximately **2.02 million product-order records** were analyzed.
- The customer-level dataset contained **105,273 unique customers**.
- The analysis covered **134 unique products**.
- The transaction data contained 200,000 unique orders.

## Technologies and Libraries

- **Apache Spark / PySpark** — distributed data processing and transformations
- **Python** — data analysis and workflow implementation
- **Pandas** — customer-level analysis and comparison with Spark
- **NumPy** — numerical operations
- **Matplotlib and Seaborn** — exploratory data visualization
- **Scikit-learn** — feature scaling, K-Means clustering, and silhouette analysis

## Methodology

### 1. Data Loading and Inspection

- Initialized a Spark session.
- Loaded the CSV dataset using PySpark.
- Examined the dataset dimensions, schema, column names, and data types.
- Inspected sample records and summary statistics.

### 2. Data Cleaning and Preprocessing

- Identified and examined missing values.
- Handled missing `days_since_prior_order` values, assigning `-1` to represent first orders in the transaction-level dataset.
- Checked for duplicate records and invalid values.
- Validated the binary `reordered` variable and the ranges of order-day and order-hour variables.
- Examined outliers in `add_to_cart_order` and retained them because unusually large carts may represent legitimate bulk purchases.

### 3. Feature Engineering

Created additional features to capture purchasing behaviour at the order and customer levels.

**Order-level feature**
- `total_items_per_order` — number of products associated with an order.

**Customer-level features**
- `total_orders` — number of unique orders placed by a customer.
- `total_products_purchased` — total product records associated with a customer.
- `average_days_between_orders` — average interval between orders.
- `average_basket_size` — average number of products per order.
- `reorder_rate` — proportion of purchased products marked as reorders.

These features were used to characterize customer purchasing frequency, basket size, purchase regularity, and repeat-purchase behaviour.

### 4. Exploratory Data Analysis (EDA)

EDA was conducted to identify patterns and understand the distributions of the engineered features.

The analysis included:
- Descriptive statistics and distribution plots.
- Histograms and boxplots for customer-level features.
- Correlation heatmaps to examine relationships between behavioural variables.
- Hourly and daily purchasing patterns.
- Department-level reorder rates.
- Customer purchase-frequency distributions.

### 5. Customer Segmentation Using K-Means

K-Means clustering was used to identify customers with similar purchasing behaviour.

The workflow included:

1. Separating first-time customers whose average inter-order interval was unavailable.
2. Preparing the remaining customer-level data for clustering.
3. Applying feature transformations and scaling.
4. Evaluating candidate cluster counts using the elbow method and silhouette scores.
5. Selecting **four K-Means clusters**, with the first-time customer segment handled separately.
6. Profiling the resulting five customer segments using purchasing and reorder metrics.

The elbow method suggested diminishing improvements in inertia beyond four clusters. The silhouette score was highest at \(k = 4\), at approximately **0.27**, indicating moderate separation with some overlap between customer segments.

### 6. Cluster Profiling and Department Analysis

The resulting customer segments were compared using:
- Average number of orders.
- Average basket size.
- Average days between orders.
- Average reorder rate.
- Product department purchasing shares.

This analysis helped distinguish customers who purchase frequently, customers with smaller baskets, infrequent shoppers, and first-time shoppers.

### 7. Spark and Pandas Comparison

A simple transformation filtering reordered products and selecting relevant columns was implemented using both PySpark and Pandas.

The notebook compared the measured transformation-definition times to illustrate the execution characteristics of the two approaches. These timings represent the specific operations and execution environment used in the notebook, rather than a general performance benchmark.

## Key Findings

### Customer Segmentation

The analysis identified five behavioural segments, including a separately handled first-time customer group.

| Cluster | Customer Segment | Customers | Avg. Orders | Avg. Basket Size | Avg. Reorder Rate |
|---|---|---:|---:|---:|---:|
| 0 | Infrequent Shoppers | 21,772 | 1.31 | 11.36 | 39% |
| 1 | Frequent Shoppers | 26,725 | 1.27 | 11.63 | 36% |
| 2 | Highly Frequent Shoppers | 30,144 | 3.51 | 13.06 | 69% |
| 3 | Small Basket Shoppers | 20,277 | 1.26 | 5.43 | 87% |
| 4 | First-Time Shoppers | 6,355 | 1.00 | 9.99 | 0% |

*Note: The first-time segment was separated based on missing average inter-order intervals. Reorder rates reflect the transaction data available for each segment.*

### Major Insights

- **Highly Frequent Shoppers:** This was the largest segment, with 30,144 customers. It recorded the highest average order count (3.51), the largest average basket size (13.06), and a reorder rate of 69%.
- **Small Basket Shoppers:** This segment had the smallest average basket size (5.43) but the highest observed reorder rate (87%), indicating strong repeat-purchase behaviour despite smaller baskets.
- **Infrequent Shoppers:** Customers in this segment had the longest average interval between orders, at 26.91 days.
- **First-Time Shoppers:** A total of 6,355 customers had only one recorded order and no available inter-order interval.
- **Reorder Behaviour:** Approximately 59% of product-order records were marked as reorders, while 41% were first-time purchases of the respective products.
- **Department Patterns:** Produce and Dairy & Eggs accounted for substantial shares of purchases across customer segments. Department shares varied relatively little between clusters.
- **Temporal Patterns:** Order activity was highest around the earlier day-of-week codes and remained concentrated in daytime hours. The dataset's day-code mapping was not documented in the notebook, so specific weekday interpretations should be treated cautiously.

## Business Implications

The customer segments suggest several opportunities for targeted marketing:

- **Highly Frequent Shoppers:** Use loyalty programmes, personalized recommendations, and relevant cross-selling offers to encourage continued engagement.
- **Small Basket Shoppers:** Recommend complementary products and use relevant bundle offers to increase basket size without undermining their existing repeat-purchase behaviour.
- **Infrequent Shoppers:** Consider personalized reminders and targeted re-engagement campaigns to encourage more regular purchases.
- **First-Time Shoppers:** Focus on onboarding, post-purchase engagement, and incentives that encourage a second order.
- **Department-Level Marketing:** Prioritize commonly purchased departments for relevant product recommendations while using customer-specific behaviour to personalize campaigns.

These are potential applications of the observed patterns; the notebook does not establish the causal effect of any marketing intervention.

## How to Run the Project

1. Clone or download this repository.
2. Open `Big_data_final(1).ipynb` in Google Colab or a compatible Jupyter environment.
3. Obtain the dataset from the Kaggle source linked above.
4. Upload the dataset to Google Drive or update the file path in the notebook.
5. Install or enable the required Python libraries and ensure PySpark is available.
6. Execute the notebook cells sequentially to reproduce the data processing, visualizations, clustering, and timing comparison.

**Note:** The notebook currently uses Google Colab and Google Drive paths. Update these paths when running the project in a different environment.

## Repository Structure

```text
E-Commerce-Customer-Behaviour-Analysis/
│
├── README.md
└── Big_data_final(1).ipynb
```

The dataset is obtained separately from Kaggle and is not included in this repository structure.

## Conclusion

This project demonstrates how big data processing and unsupervised machine learning can be combined to analyze e-commerce purchasing behaviour. Using PySpark for transaction-level processing and K-Means for customer segmentation, the analysis identifies distinct purchasing patterns and highlights opportunities for customer retention, personalized recommendations, and targeted marketing.

The findings illustrate the value of behavioural segmentation in transforming large-scale transaction data into actionable business insights.

## Author

**Ananya** <br>
**Ayushi**

## Acknowledgements

- [Kaggle — E-Commerce Consumer Behaviour Dataset](https://www.kaggle.com/datasets/hunter0007/ecommerce-dataset-for-predictive-marketing-2023/data)
- Apache Spark and PySpark documentation
- Scikit-learn documentation

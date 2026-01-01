# CustomerEngagementIntelligencePipeline

## Business Problem
Marketing and retention team need ways to identify:
* High value customers to retain and reward
* Customers losing engagement and at risk of churn

## Project Goal
To build a scalable data pipeline using PySpark that transforms raw customer, order and web event data into actionable insights for marketing and stakeholders. This is delovered through Power BI dashboard.

## Data sources
Following are the data sources used and they are of various formats:
* Customer Profile - Parquet format
  This dataset contains the Customer Id, country and signup date
  
* Orders - Parquet format
  This dataset contains Order Id, Customer Id, Order Date and Order Amount
  
* Web events of customers - JSON format
  This dataset contains Customer Id, Event Time and Event Type

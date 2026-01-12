# CustomerEngagementIntelligencePipeline

## Business Problem
Marketing and retention team need ways to identify:
* High value customers to retain and reward
* Customers losing engagement and at risk of churn

## Project Goal
Build an end-to-end customer engagement data pipeline using PySpark that transforms raw customer, order, and web event data into actionable, analytics-ready insights.

The pipeline is designed with production-style practices, including:

* Data quality and integrity checks

* Event sessionization logic

* Reproducible analysis using a fixed as-of date

* Explicit handling of inactive and edge-case customers

The final output is optimized for downstream BI consumption and customer-level engagement analysis.

## Data Disclaimer

Synthetic Data Notice

All datasets used in this project are synthetically generated and designed to simulate realistic customer, transaction, and web behavior patterns.
No real customer data, personally identifiable information (PII), or proprietary business data is used.

The final output is optimized for downstream BI consumption and customer engagement analysis.

## Data Sources
Following are the data sources used and they are of various formats:
* Customer Profile - Parquet format

  This dataset contains the Customer Id, country and signup date
  
* Orders - Parquet format

  This dataset contains Order Id, Customer Id, Order Date and Order Amount
  
* Web events of customers - JSON format

  This dataset contains Customer Id, Event Time and Event Type

## Data Pipeline Architecture
1. Data Ingestion
   
   * Reads Parquet and JSON datasets using Spark.
   * Recursive file lookup enabled to support partitioned data
   * Schema inspection after the load

2. Data Quality and Integrity Checks
   
   The pipeline enforces data validation rules before transformation:
   * Null checks on primary identifiers
   * Referential integrity checks to prevent orphan orders or events
   * Validation of non-negative order amounts
   * Verification of one-row-per customer grain
  
3. Web Sessionization

   Web Events are sessionized using Spark window functions
   * Events are partitioned by customer_id
   * Events ordered by timestamp
   * A new session starts if the time between two consecutive events is more than 30 mins

4. Transaction Aggregation

   Orders are aggregated to the customer level:
   * Total Number of orders
   * Total Revenue
   * Most recent Order Date

   This supports the RFM analysis patterns.

5. Consistency Checks

   * Customers with signup dates after the last observed event date are evaluated
   * Orders placed after the analysis date are checked
   * Ensures all entities fall within the observation window.
  
6. Customer Level Dataset Creation

   * Customer, order, and session datasets are joined at customer grain
   * Missing values are handled

7. Engagement Feature Engineering

   The pipeline computes engagement features:
   * Recency: Days since last order (relative to as-of-date)
   * Order Frequency
   * Session Activity
   * Revenue
   * Min-Max normalization applied to all engagement components

8. Engagement Scoring and Segmentation

   A weighted engagement score is computed:
   * Recency (40%)
   * Frequency (30%) (Order Frequency and Session Acitvity)
   * Monetary Value (30%)
  
   Additional Fields:
   * Engagement Segment (High/ Medium/ Low/ At Risk)
   * Engagement Status (Active, Browsing Only, Not Active)
  
9. Performance Optimization

   The final customer engagement dataset is presisted using Memory and Disk

10. Final Validation

    Before export, the pipelines checks:
    1. One row per customer
    2. Engagement score range validation between (0-1)
    3. Null safety
   
## Power BI Dashboard
The dashboard progresses from high-level engagement health to explainability, segmentation, churn risk, and finally individual customer drill-down, enabling both strategic and tactical decision-making.

### 1. Executive Summary:

The executive summary provides a high-level view of customer engagement health.

<img width="1252" height="710" alt="ExecutiveSummary" src="https://github.com/user-attachments/assets/f9144187-34bd-4d76-b116-29be2c5969ca" />


### 2. Customer Value vs Engagement Risk:

This page helps marketing and retention teams prioritize customers by balancing business value (revenue) against engagement risk, so actions can be focused where impact is highest.

<img width="1240" height="700" alt="CustomerValue" src="https://github.com/user-attachments/assets/e141cc96-042e-4c41-9a98-8021cc7b2f42" />


### 3. At-Risk Customers:

This page isolates active customers with declining engagement, quantifies the revenue at risk, explains the behavioral drivers using recency, and provides a ready-to-use target list for retention campaigns.

<img width="1247" height="711" alt="AtRisk" src="https://github.com/user-attachments/assets/378783c1-67c5-4e62-b387-0db685867908" />


### 4. High Value Customers:

This page identifies high value customers who are both revenue-significant and strongly engaged. It enables proactive retention and reward strategies.

<img width="1248" height="696" alt="high value" src="https://github.com/user-attachments/assets/6b36f994-b2e7-40ee-80bd-647262f75b4b" />


### 5. Behavior Insights:

The Behavior Insights page explores behavioral drivers behind engagement scores by analyzing session frequency, purchase behavior, and their relationships, providing explainability to the engagement model.

<img width="1256" height="576" alt="behavior" src="https://github.com/user-attachments/assets/2f479034-468a-4c50-b3fe-3fc50b698366" />


## Tech Stack
* PySpark (Spark SQL, Window Functions)
* Google Colab (execution environment)
* Parquet & JSON (source data formats)
* CSV (analytics-ready output for BI tools)
* Power BI (Visualization and analysis)

## Potential Advanced Studies
* Advanced behavioral segmentation
* Personalized campaign targeting
* Customer journey analysis
* Automated churn detection and win-back workflows

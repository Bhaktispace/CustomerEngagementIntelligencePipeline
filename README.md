# CustomerEngagementIntelligencePipeline

## Business Problem
Marketing and retention team need ways to identify:
* High value customers to retain and reward
* Customers losing engagement and at risk of churn

## Project Goal
To build a data pipeline using PySpark that transforms raw customer, order and web event data into actionable insights for marketing and stakeholders. The pipeline is designed with production-style practices including data quality checks, sessionization logic, reproducibility controls, and explicit handling of edge cases.

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
   * One-row-per customer grain
  
3. Web Sessionization

   Web Events are sessionized using Spark window functions
   * Events are partitioned by customer_id
   * A new session starts if the time between two consecutive events is more than 30 mins

4. Transaction Aggregation

   Customer level aggregation:
   * Total Number of orders
   * Total Revenue
   * Most recent Order DAte

   This supports the RFM analysis patterns.

5. Consistency Checks

   * Customers with signup dates after the last observed event date are evaluated
   * Orders placed after the analysis date are checked
   * Ensures all entities fall within the observation window.
  
6. Customer Level Dataset Creation

   All the datasets are joined at customer grain to ensure inclusion of inactive users.
   
   Missing values are handled.

7. Engagement Feature Engineering

   The pipeline computes engagement features:
   * Recency: Days since last order (relative to as-of-date)
   * Order Frequency
   * Session Activity
   * Revenue

   Min-Max normalization is applied.

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
   
  
## Output

## Analytics and Visualization

## Tech Stack
* PySpark (Spark SQL, Window Functions)
* Google Colab (execution environment)
* Parquet & JSON (source data formats)
* CSV (analytics-ready output for BI tools)

## Potential Advanced Studies
* Advanced segmentation Analysis: Create a highly targeted segments based on user behavor
* Personalized Campaigns: Deliver real-time, tailored messages across multiple channels like push notification, in-app messages and emails
* Behavioral Analytics: Gain deep insights into customer journeys and behavior, helping optimize marketing strategies
* Automation: Identify at-risk users and engage them with personalized win-back campaigns.

## Power BI Dashboard
The dashboard progresses from high-level engagement health to explainability, segmentation, churn risk, and finally individual customer drill-down, enabling both strategic and tactical decision-making.

Create a behaviorally aware engagement score at the customer level by combining:
* Purchase recency
* Purchase frequency
* Web engagement activity
* Monetary value

This score will be used to identify:
* High-value customers
* At-risk customers
* Highly engaged non-buyers

1. Executive Summary:

The executive summary provides a high-level view of customer engagement health.

2. Customer Value vs Engagement Risk:

This page helps marketing and retention teams prioritize customers by balancing business value (revenue) against engagement risk, so actions can be focused where impact is highest.

This page provides a high level check of customer engagement.

3. At-Risk Customers:

This page isolates active customers with declining engagement, quantifies the revenue at risk, explains the behavioral drivers using recency, and provides a ready-to-use target list for retention campaigns.

4. High Value Customers:

This page identifies high value customers who are both revenue-significant and strongly engaged. It enables proactive retention and reward strategies.

5. Behavior Insights:

The Behavior Insights page explores behavioral drivers behind engagement scores by analyzing session frequency, purchase behavior, and their relationships, providing explainability to the engagement model.

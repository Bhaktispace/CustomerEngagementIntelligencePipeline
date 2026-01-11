# CustomerEngagementIntelligencePipeline

## Business Problem
Marketing and retention team need ways to identify:
* High value customers to retain and reward
* Customers losing engagement and at risk of churn

## Project Goal
To build a scalable data pipeline using PySpark that transforms raw customer, order and web event data into actionable insights for marketing and stakeholders. The pipeline is designed with production-style practices including data quality checks, sessionization logic, reproducibility controls, and explicit handling of edge cases.

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

   Min-Max normalization is applied .

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

## PHASE 8️⃣ – Power BI Dashboard (What to Build)
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

Executive Summary:

The executive summary provides a high-level view of customer engagement health.

Customer Value vs Engagement Risk:

This page helps marketing and retention teams prioritize customers by balancing business value (revenue) against engagement risk, so actions can be focused where impact is highest.

This page provides a high level check of customer engagement.

At-Risk Customers:

This page isolates active customers with declining engagement, quantifies the revenue at risk, explains the behavioral drivers using recency, and provides a ready-to-use target list for retention campaigns.


High Value Customers:

This page identifies high value customers who are both revenue-significant and strongly engaged. It enables proactive retention and reward strategies.

Behavior Insights:

The Behavior Insights page explores behavioral drivers behind engagement scores by analyzing session frequency, purchase behavior, and their relationships, providing explainability to the engagement model.


1️⃣ Executive Overview (Start Here)
🎯 Purpose

High-level health check of customer engagement.

Key Questions Answered

How engaged is our customer base overall?

Are we growing or losing engagement?

How many customers are at risk?

Visuals

KPI cards:

Avg Engagement Score

% High-Risk Customers

Total Customers

Avg Sessions (30 Days)

Engagement score distribution (histogram)

Customer segmentation donut:

High / Medium / Low engagement

Trend: Avg engagement score over time (if snapshot history exists)

Why It’s Strong

✔ Business-first
✔ C-level friendly
✔ Immediate impact

2️⃣ Engagement Score Breakdown
🎯 Purpose

Explain why customers have the scores they do.

Key Questions

What drives engagement most?

Are customers engaged via browsing or buying?

Are recent sessions translating to purchases?

Visuals

Scatter plot:

X: Sessions last 30 days

Y: Total orders

Size: Total revenue

Bar chart:

Avg recency, frequency, monetary scores

Decomposition tree:

Engagement Score → Recency / Frequency / Monetary

Why It’s Strong

✔ Transparent modeling
✔ Shows feature engineering thinking
✔ Avoids “black box” scoring

3️⃣ Customer Segmentation
🎯 Purpose

Identify actionable customer groups.

Segments

High Engagement / High Revenue (VIPs)

High Engagement / Low Orders (Converters-to-be)

Low Engagement / High Revenue (At-risk VIPs)

Low Engagement / Low Revenue (Dormant)

Visuals

Matrix:

Engagement band × Revenue band

Clustered bar:

Customers by segment

Table with drill-through:

Customer ID

Engagement score

Sessions

Orders

Revenue

Why It’s Strong

✔ Marketing-ready
✔ Retention-focused
✔ Highly actionable

4️⃣ At-Risk Customers (Churn Focus)
🎯 Purpose

Identify customers who need intervention.

Definition Used

Low engagement score

Low sessions

No recent orders

Visuals

KPI: Total at-risk customers

Trend: At-risk % over time

Table:

Customer ID

Last order date

Sessions last 30 days

Engagement score

Conditional formatting (red/yellow/green)

Why It’s Strong

✔ Clear churn story
✔ Easy business adoption
✔ Perfect for stakeholder demos

5️⃣ Behavioral Analysis (Web Events)
🎯 Purpose

Understand browsing behavior vs conversion.

Key Questions

Are customers browsing but not buying?

Where are drop-offs happening?

Are sessions meaningful?

Visuals

Funnel:

Page View → Add to Cart → Checkout → Order

Avg sessions vs avg orders (line or bar)

Heatmap:

Engagement score vs session count

Why It’s Strong

✔ Uses your web events data
✔ Connects Spark sessionization to BI
✔ Shows full-funnel thinking

6️⃣ Customer Drill-Through Page (Advanced but Impressive)
🎯 Purpose

Deep-dive into individual customers.

Activated By

Clicking customer ID anywhere else

Visuals

Customer profile summary

Order history timeline

Session trend (last 30 days)

Engagement score components

Why It’s Strong

✔ Shows model explainability
✔ Feels “real-world”
✔ Very impressive for portfolios

🧭 Optional (If You Want to Go Further)
7️⃣ Data Quality & Model Monitoring

% customers with nulls

Score distribution stability

Outlier detection

This screams senior-level but is optional.

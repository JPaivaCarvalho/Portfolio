# Performance Dashboard - Digital Assistant Tool For electrical installation projects
### Looker Studio + Google BigQuery | Data Source Migration

---

## 📋 Project Overview

This project involved the development and implementation of a comprehensive performance dashboard for a digital tool designed mainly for electricians and electrical installers, available on mobile platforms (iOS and Android) and desktop (Windows and macOS). The dashboard was built using **Google Looker Studio** and **Google BigQuery** as the primary data source, enabling real-time visualisation of critical business metrics and user behaviour.

**Client Context:**
- **Sector:** Technology / Digital Applications
- **Product Type:** Multi-platform personal digital assistant
- **Team:** Development in partnership with product installers
- **Target Audience:** End users on mobile and desktop devices

---

## 🎯 Project Objectives

1. **Centralise performance data** from the digital assistant into a single interactive dashboard
2. **Migrate the data source** from Google Analytics to Google BigQuery
3. **Ensure data reliability** and minimise access interruptions
4. **Provide actionable insights** on user behaviour, engagement, and retention
5. **Enable comparative analysis** across platforms (mobile vs desktop)

---

## 🔄 Main Challenge: GA → BigQuery Migration

### **Identified Problem**

The initial dashboard used **Google Analytics as a direct data source** in Looker Studio. However, we faced a critical challenge:

- **Google Analytics OAuth authentication tokens expired frequently** ("broke")
- This caused **data access interruptions** and visualisation failures in the dashboard
- Constant manual maintenance of reconnections became unsustainable
- Negative impact on the **reliability and availability** of the dashboard for stakeholders

### **Implemented Solution**

To resolve this structural problem, I implemented a **complete data source migration to Google BigQuery**:

#### **Data Architecture**

```
Google Analytics (Tracking)
        ↓
BigQuery (Data Warehouse)
        ↓
Looker Studio (Visualisation)
```

#### **Migration Process**

1. **BigQuery Export Setup**: Activation of automatic data export from Google Analytics to BigQuery
2. **Data Modelling**: Creation of optimised SQL queries to replicate GA metrics
3. **Data Transformation**: Development of views and calculated tables in BigQuery for custom metrics
4. **Looker Studio Connection**: Replacement of the GA data source with a native BigQuery connection
5. **Validation and Reconciliation**: Comparison of metrics between GA and BigQuery to ensure consistency

#### **Migration Benefits**

✅ **Stability**: Complete elimination of the token expiration problem  
✅ **Performance**: Optimised SQL queries with BigQuery caching  
✅ **Flexibility**: Ability to create custom and complex metrics  
✅ **Scalability**: Prepared for data volume growth  
✅ **Control**: Greater autonomy over data transformations and aggregations  

---

## 📊 Implemented Metrics

The dashboard implements a comprehensive set of metrics organised into three main dimensions:

### **1. Acquisition & Usage Metrics**

| Metric | Description | SQL Query |
|---|---|---|
| **Total Users** | Unique number of active users | `COUNT(DISTINCT user_pseudo_id)` |
| **Total Sessions** | Total recorded sessions | `COUNT(DISTINCT session_id)` |
| **New Users** | Users who started their first session | `COUNT(DISTINCT CASE WHEN is_first_visit = TRUE THEN user_pseudo_id END)` |
| **Sessions per User** | Average sessions per user | `COUNT(session_id) / COUNT(DISTINCT user_pseudo_id)` |

### **2. Engagement Metrics**

| Metric | Description | Implementation |
|---|---|---|
| **Engagement Rate** | % of sessions with significant engagement | Sessions with > 10s or > 2 events |
| **Average Session Duration** | Average session duration | Calculation based on event timestamps |
| **Events per Session** | Average events per session | `COUNT(event_name) / COUNT(DISTINCT session_id)` |
| **Bounce Rate** | % of sessions with only 1 page/screen | Sessions with a single view event |
| **Pages/Screens per Session** | Average pages viewed per session | Count of `page_view` / `screen_view` events |

### **3. Retention & Device Metrics**

| Dimension | Calculated Metrics |
|---|---|
| **User Retention** | Cohort analysis by acquisition week |
| **Device Breakdown** | Distribution by platform (iOS, Android, Windows, macOS) |
| **Platform Analysis** | Metric comparison between Mobile App vs Desktop App |
| **Operating System** | Detailed breakdown by OS version |

---

## 📸 Dashboard Screenshots

### Dashboard 1: Global Overview — Key Metrics

![Dashboard Global Overview](https://i.imgur.com/JnwtmdT.png)

**Features:**
- Main KPIs in highlighted cards at the top
- Installs, uninstalls and net growth trend lines
- Store rating gauge and breakdown by country
- Unique users by source/medium and device category
- Account creation, logins, new users, active users, and engagement time cards

---

### Dashboard 2: Users & Sessions Analysis

![Dashboard Users and Sessions](https://i.imgur.com/75WR8Pn.png)

**Features:**
- Authenticated vs non-authenticated user breakdown
- Users per platform (desktop, mobile, tablet)
- Sessions KPIs: total sessions, authenticated sessions, average session duration
- Progression charts for users and sessions over time
- Downloads section with installs and net growth

---

### Dashboard 3: Market / Basket Transfer Value

![Dashboard Market](https://i.imgur.com/tre6CMp.png)

**Features:**
- Rolling 12-month data: See Shopping List, Export Shopping List, Order Online
- Data during selected period with the same KPIs
- Basket Transfer Value: total and average basket value
- Wholesaler breakdown table with Order Online counts
- Pie chart distribution by wholesaler

---

### BigQuery — Events Table (SQL Query)

![BigQuery Events Query](https://i.imgur.com/ll3q6bF.png)

SQL query used to create the `Mobile_data_eventos` table, partitioned by `event_date`, aggregating event parameters and user properties from the raw GA4 export.

---

### BigQuery — Sessions Table (SQL Query)

![BigQuery Sessions Query](https://i.imgur.com/RFKdzzC.png)

SQL query used to create the `Mobile_data_sessoes` table, partitioned by `date`, reconstructing sessions from raw events with custom logic for best `user_id` selection, session duration, and engagement metrics.

---

### BigQuery — Events Table Preview

![BigQuery Events Table Preview](https://i.imgur.com/YmaWs4H.png)

Preview of the `Mobile_data_eventos` table in BigQuery, showing columns such as `session_id`, `app_language`, `data_package_language`, `active_user`, `traffic_source`, and `traffic_medium`.

---

### BigQuery — Sessions Table Preview

![BigQuery Sessions Table Preview](https://i.imgur.com/4J2a6ZL.png)

Preview of the `Mobile_data_sessoes` partitioned table in BigQuery, showing columns such as `date`, `user_id`, `user_pseudo_id`, `session_id`, `session`, `session_start`, and `session_end`.

---

## 🛠️ Technologies Used

### **Platforms & Tools**

- **Google BigQuery**: Data warehouse and SQL query processing
- **Google Looker Studio**: Visualisation and dashboarding platform
- **Google Analytics 4 (GA4)**: Event tracking and user behaviour


### **Sample SQL Queries**

#### Engagement Rate Calculation
```sql
WITH session_engagement AS (
  SELECT
    session_id,
    COUNTIF(event_name != 'session_start') > 2 
      OR MAX(engagement_time_msec) / 1000 > 10 AS is_engaged
  FROM `project.dataset.events_*`
  WHERE _TABLE_SUFFIX BETWEEN '20240101' AND '20240131'
  GROUP BY session_id
)
SELECT
  COUNTIF(is_engaged) / COUNT(*) * 100 AS engagement_rate
FROM session_engagement;
```

#### Platform Breakdown
```sql
SELECT
  device.category AS platform,
  COUNT(DISTINCT user_pseudo_id) AS users,
  COUNT(DISTINCT session_id) AS sessions,
  ROUND(AVG(engagement_time_msec) / 1000, 2) AS avg_session_duration_sec
FROM `project.dataset.events_*`
WHERE _TABLE_SUFFIX BETWEEN FORMAT_DATE('%Y%m%d', DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY))
  AND FORMAT_DATE('%Y%m%d', CURRENT_DATE())
GROUP BY platform
ORDER BY users DESC;
```

---


### **Business Impact**

📊 **Data democratisation**: Stakeholders have self-service access to real-time metrics  
🎯 **Data-driven decisions**: Identification of usage patterns that informed the product roadmap  
📱 **Platform insights**: Discovery that desktop users have 45% more engagement than mobile, informing development strategy  
🔄 **Retention optimisation**: Identification of drop-off points in the user journey that were subsequently optimised  

---

## ⚠️ Notes on Data Discrepancies

### **Expected Differences Between GA4 Interface and BigQuery**

It is important to document that small discrepancies between Google Analytics interface metrics and BigQuery are **expected and normal**:

#### **1. Data Sampling**
- **GA Interface**: May apply sampling in reports with high data volumes
- **BigQuery**: Works with 100% of exported data (raw data)
- **Impact**: BigQuery tends to be more accurate at high volumes

#### **2. Real-Time Processing**
- **GA Interface**: Includes real-time data (last 24–48h may still be processing)
- **BigQuery Export**: Data is exported with a ~24h delay (D-1 data)
- **Impact**: Temporal differences in "today" or "last 24h" analyses

#### **3. Metric Definitions**
- **Sessions**: GA uses proprietary logic; BigQuery requires manual reconstruction based on `ga_session_id`
- **Engagement Time**: Calculations may vary depending on `engagement_time_msec` aggregation
- **Bounce Rate**: Definition changed in GA4; requires custom logic in BigQuery

#### **4. Filters & Exclusions**
- **GA Interface**: Automatically applies view filters (e.g. internal traffic, bots)
- **BigQuery**: Raw data without filters; requires manual application of exclusions
- **Impact**: BigQuery may show slightly higher values if filters are not replicated

### **Reconciliation Recommendations**

1. **Accept a margin of variation**: Differences of 1–3% are considered normal and acceptable
2. **Use BigQuery as the source of truth**: For historical and detailed analyses
3. **Document definitions**: Maintain clear documentation of how each metric is calculated
4. **Periodic validation**: Compare monthly samples to ensure alignment

### **Implementation Notes Added to the Dashboard**

A "Methodology" section was included in the Looker Studio dashboard itself, explaining:
- Data source (BigQuery vs GA)
- Key metric definitions
- Data refresh period
- Known limitations





---

**Last updated:** April 2026  
**Project Duration:** 3 months (design, implementation and optimisation)  
**Status:** In production and ongoing maintenance

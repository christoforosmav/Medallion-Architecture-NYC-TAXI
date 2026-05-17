# 🚖 NYC Taxi & Weather Data Engineering Pipeline (11M+ Rows)

An end-to-end **Batch ETL Pipeline** implemented on **Azure Databricks** using **PySpark** and **SQL**, processing over 11 million rows of real-world urban data. The project uncovers hidden correlations between New York City transit patterns and historical weather conditions.

## 🏗️ Project Architecture (Medallion Framework)
The data pipeline implements the industry-standard Medallion Architecture to transition data through various stages of refinement:

1. **Bronze Layer (Raw Ingestion):** Ingested raw, immutable formats into Azure Databricks Volumes. This includes high-frequency NYC Yellow Taxi trip logs (Parquet format) and historical weather records (CSV format) retrieved from the Open-Meteo REST API.
2. **Silver Layer (Cleaning & Transformation):** Performed data quality enforcement and deduplication using PySpark. Eliminated over **3.2 million anomalous records** (zero distances, invalid passenger counts, and negative fares), cast column data types, and normalized timestamps to standard date formats.
3. **Gold Layer (Enrichment & Serving):** Executed an optimized distributed Inner Join between the refined taxi records and weather attributes using calendar dates as relational keys. The final analytical dataset was persisted as an ACID-compliant **Delta Table**.

## 🛠️ Tech Stack
- **Languages:** Python, SQL, PySpark SQL
- **Platform:** Azure Databricks (Serverless Compute clusters)
- **Storage & Formats:** Parquet, CSV, Delta Lake (Delta Tables)
- **Version Control:** Git Integration (GitHub Repos)

---

## 📊 Analytical Insights & Hypothesis Testing

The Gold layer was queried using Spark SQL to evaluate core business hypotheses regarding urban dynamics and consumer behavior during weather shocks.

### 🔍 Hypothesis 1: Weather Impact on Passenger Tipping Patterns
* **Core Objective:** Investigate whether inclement weather increases passenger gratitude due to the difficulty of securing a carriage, thereby driving higher average tips.

#### 📈 Empirical Findings:
- **No Rain (Dry Weather):** 3.75M rides | Avg Tip: **$3.64**
- **Drizzle / Light Rain:** 216K rides | Avg Tip: **$3.62**
- **Storm / Heavy Rain:** 1.88M rides | Avg Tip: **$3.57**

* **Conclusion:** *Hypothesis Rejected.* Average tips systematically decrease as precipitation intensity grows. This ~2% drop during heavy storms is primarily driven by higher total fare amounts accumulating from heavy gridlocks, alongside increased passenger frustration caused by weather-induced transit delays.

---

### 📍 Hypothesis 2: Weather Impact on Urban Traffic Velocity (The Traffic Paradox)
* **Core Objective:** Determine if severe precipitation paralyzes New York City's infrastructure, leading to extended trip durations and lower average speeds.

#### 📈 Empirical Findings:
- **Dry Weather:** 3.67M rides | Avg Duration: **17.76 mins** | Avg Speed: **10.41 mph**
- **Rain / Snow:** 3.97M rides | Avg Duration: **16.95 mins** | Avg Speed: **11.19 mph**

* **Conclusion:** *The Traffic Paradox Confirmed.* Taxis operate **~7.5% faster** with shorter overall trip durations during storm and snow conditions. The data indicates that severe winter weather suppresses casual and private vehicle volume on the streets. This leaves the roads clearer for seasoned, professional taxi drivers to navigate the city grid more efficiently.

---

## 💻 Code Sample: Silver Layer Data Quality Gate
```python
# Enforcing strict data quality parameters on the Bronze Dataframe
taxis_silver_df = taxis_df.filter(
    (col("tpep_pickup_datetime") >= "2026-01-01 00:00:00") & 
    (col("tpep_pickup_datetime") <= "2026-03-31 23:59:59") & 
    (col("trip_distance") > 0) & 
    (col("total_amount") > 0) &
    (col("passenger_count") > 0)
)

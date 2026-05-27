# 🚒 West Midlands Fire Service (WMFS) Operational Intelligence Dashboard

![Dashboard Executive Overview](Hero_Dashboard.png)
*(Above: Page 1 - Executive Strategy Overview, showing where and when regional resources are under the most pressure.)*

## 🎯 Project Overview
As a Civil Engineer based in Tipton, I wanted to investigate how local public services manage their physical assets and response times. I used a public dataset from the Birmingham City Observatory containing **449,490 historical emergency incidents** logged by the West Midlands Fire Service (WMFS).

*   **The Goal:** Build an interactive, two-page Power BI dashboard to show where resources are being deployed efficiently and, more importantly, where they are being wasted.
*   **The ROI:** Identified a massive **37.35% False Alarm Rate** across the region (representing ~167,000 wasted call-outs). Normalising this data allows local councils to target specific problem areas, potentially saving thousands of pounds in unnecessary vehicle wear and fuel costs.

---

## 💡 What the Data Shows (Operational Insights)
*Summary: Translating raw incident counts into practical, local solutions.*

### 1. The Regional Waste Leaderboard (Normalised Data)
If we only look at raw numbers, Birmingham always appears to be the problem because of its size. By normalising the data into a percentage rate, I identified that **Wolverhampton** is actually the highest operational drain, with **42.22%** of all dispatches being false alarms, followed closely by Birmingham (39.14%).
![Wolverhampton Waste](Wolverhampton_Waste.png)

### 2. Temporal Demand (The 24-Hour Pulse)
By grouping the incident times into hourly buckets, I isolated a clear "bimodal" peak in demand. The network is under the highest pressure at **17:00 (5 PM - 2.7M movements)** and **08:00 (8 AM - 2.6M movements)**. This peak hour analysis directly informs crew shift-scheduling and vehicle readiness.
![Temporal Pulse](Temporal_Pulse.png)

### 3. Tactical Investigation (Root Cause Analysis)
On Page 2, I built an interactive **Decomposition Tree** that allows managers to drill down into the data [9, 10]. 
*   **The Discovery:** Selecting **Sandwell** and drilling into **False Alarms (Equipment)** reveals that **Health Properties** (specifically Local Authority Psychiatric Hospitals and Health Centres) are the primary drivers of false alarms in our local borough, rather than standard domestic homes.
![Root Cause Tree](Root_Cause_Tree.png)

---

## 🛠️ How I Built It (ETL & DAX Pipeline)

### 1. Power Query ETL (Data Cleaning)
Public government data is notoriously messy. I wrote an automated ingestion pipeline in Power Query to clean the raw 100MB+ file:
*   **Time-Series Split:** Extracted discrete `Date` and `Time` columns from a complex ISO string.
*   **Geospatial Processing:** Split combined centroid coordinate strings into decimal `Latitude` and `Longitude` values for map rendering.
*   **Null-Value Mitigation:** Replaced empty text strings with `"Unrecorded"` and transformed nulls in casualty columns to `0` to ensure calculation completeness across 449k rows.

![Power Query Pipeline](Power_Query_Pipeline.png)

### 2. Advanced DAX Calculations
I created a dedicated measures table to house the business logic, using advanced iterations to calculate resource pressure during critical, high-stakes incidents:

![DAX Measures](DAX_Calculated_Measures.png)

```dax
-- Calculating the average fleet footprint only during serious, life-safety emergencies
Avg Fleet per Critical Incident = 
VAR CriticalIncidents = FILTER(
    'wmfs-incidents', 
    'wmfs-incidents'[Injuries] > 0 || 'wmfs-incidents'[Fatalities] > 0
)
RETURN
CALCULATE(
    AVERAGEX(
        'wmfs-incidents',
        'wmfs-incidents'[PRL Count] + 'wmfs-incidents'[BRV Count] + 'wmfs-incidents'[BSV Count] + 'wmfs-incidents'[HP Count]
    ),
    CriticalIncidents
)

```
## 🏆 Skills Demonstrated

*   **Large-Scale Data Handling:** Successfully managed and visualised an operational dataset containing over **449,000 records** without performance degradation.
*   **Data Cleaning (ETL):** Designed robust, automated ingestion pipelines in Power Query to parse, clean, and standardise high-variance public sector files.
*   **Advanced Power BI & DAX:** Applied variables, context transition, and complex iterative calculations (`AVERAGEX` + `FILTER`) to model resource allocations.
*   **User-Centred Design (UI/UX):** Developed a high-contrast, dark-themed operational dashboard designed specifically for rapid decision-making (the **3-Second Rule**) [11].
*   **Operations & Logistics:** Translated raw dispatch numbers into actionable insights regarding vehicle deployment costs, fleet pressure, and asset maintenance.

---

### 🎓 Project Credentials
*   **Developer:** Katen Morker
*   **Credentials:** BEng (Hons) Civil Engineering | Certified Data Analyst
*   **Source Data:** West Midlands Fire Service (WMFS) via the Birmingham City Observatory

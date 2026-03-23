# 🏭 NorDex Manufacturing — Shift Performance Analytics and Optimisation

> **Cross-Functional Analytics Project | Power BI | DAX | Power Query | SQLite3 | Industry 4.0**

---

## 📁 Project Files

🔗 **Full Project (Dashboards, Data, Documentation, Presentation):** [Google Drive](https://drive.google.com/drive/folders/1N2BLIqKEnG_SUG0A7qtktFtwjUYiNV1e?usp=sharing)

---

## 📌 Project Overview

This project delivers a comprehensive analytics solution for **NorDex Manufacturing**, a cross-functional initiative developed collaboratively by Business Analysts, Data Analysts and Data Scientists.

The goal was to analyse shift performance across 3 production shifts, benchmark key metrics against **Industry 4.0 and automotive manufacturing standards**, and deliver actionable insights through 4 interactive Power BI dashboards.

---

## 🚨 Business Problem

- No centralised system to monitor shift performance, machine efficiency or quality metrics in real time
- Operational decisions being made without data driven insight
- Performance gaps against Industry 4.0 standards unidentified and unaddressed

---


🔗 **Original Data Source:** [Google Drive](https://drive.google.com/file/d/1JOyqdfCbdGJODTWUXQ8BqCQprSrLy_d8/view?usp=drive_link)

---

## 🗂️ Data Model — Star Schema

### Fact Tables
| Table | Granularity | Key Measures |
|-------|-------------|--------------|
| `Production_Log` | Per production event | units_produced, defect_count, cycle_time_avg |
| `Machine_Log` | Per machine activity | runtime_hours, downtime_minutes |
| `Maintenance_Records` | Per maintenance event | downtime_minutes, issue_type |
| `Quality_Control` | Per quality inspection | inspection_result, defect_type, severity |
| `Environmental_Data` | Per environmental reading | temperature, humidity |

### Dimension Tables
| Table | Primary Key | Describes |
|-------|-------------|-----------|
| `Shift_Master` | shift_id | Shifts |
| `Machine_Dim` | machine_id | Machines |
| `Operator_Assignment` | operator_id | Operators |

---

## 🧹 Data Cleaning & Transformation

All cleaning and transformation was done in **Power Query (M Language)**:

- ✅ Removed null primary keys from `Maintenance_Records`
- ✅ Replaced null dates with `1900-01-01` placeholder in `Production_Log`
- ✅ Removed duplicates using all columns in `Machine_Log`
- ✅ Disabled flat file from loading to prevent ambiguous relationships
- ✅ Added `date` column to `Machine_Log` from source for correct slicer filtering
- ✅ Created `machine_shift_key` composite key for data integrity
- ✅ Built Date dimension table for time intelligence

---

## 📐 KPI Framework

All KPIs benchmarked against **Industry 4.0 and Automotive Manufacturing Standards**:

| KPI | Formula | 🟢 Green | 🟡 Yellow | 🔴 Red |
|-----|---------|---------|---------|------|
| OEE % | Availability x Performance x Quality | ≥80% | 70-79% | <70% |
| Availability % | (Planned Time - Downtime) / Planned Time | ≥90% | 80-89% | <80% |
| Performance % | Actual Output / Theoretical Output | ≥95% | 85-94% | <85% |
| Quality % | (Units - Defects) / Units | ≥97% | 94-96% | <94% |
| Labour Utilisation % | Runtime Hours / Planned Hours | ≥88% | 80-87% | <80% |
| Downtime per Shift | Total Downtime / Shifts | ≤30 mins | 30-60 mins | >60 mins |
| Defect Rate % | Defects / Units Produced | ≤1% | 1-2% | >2% |
| First Pass Yield % | Pass Inspections / Total | ≥97% | 94-96% | <94% |
| Rework Rate % | Rework / Total Units | ≤1.5% | 1.5-3% | >3% |

---

## ⚙️ Key DAX Measures

```dax
-- Availability %
Availability % = 
VAR PlannedTimeMinutes = COUNTROWS(Machine_Log) * 480
VAR TotalDowntimeMinutes = SUM(Machine_Log[downtime_minutes])
RETURN DIVIDE(PlannedTimeMinutes - TotalDowntimeMinutes, PlannedTimeMinutes)

-- Performance %
Performance % = 
DIVIDE(
    SUM(Production_Log[units_produced]),
    COUNTROWS(Production_Log) * 1000
)

-- Quality %
Quality % = 
DIVIDE(
    SUM(Production_Log[units_produced]) - SUM(Production_Log[defect_count]),
    SUM(Production_Log[units_produced])
)

-- OEE %
OEE % = 
VAR Avail = [Availability %]
VAR Perf  = [Performance %]
VAR Qual  = [Quality %]
RETURN Avail * Perf * Qual

-- Defect Rate %
Defect Rate % = 
DIVIDE(
    SUM(Production_Log[defect_count]),
    SUM(Production_Log[units_produced])
)

-- Labour Utilisation %
Labour Utilisation % = 
DIVIDE(
    SUM(Machine_Log[runtime_hours]),
    COUNTROWS(Machine_Log) * 8
)
```

---

## 📈 Key Findings

### Overall Performance vs Industry 4.0 Benchmarks

| KPI | Actual | Target | Status |
|-----|--------|--------|--------|
| OEE % | 54.92% | 80% | 🔴 Critical |
| Availability % | 89.71% | 90% | 🟡 Warning |
| Performance % | 64.42% | 95% | 🔴 Critical |
| Quality % | 96.86% | 97% | 🟡 Warning |
| Defect Rate % | 3.09% | 1% | 🔴 Critical |

### Shift Performance Comparison

| Shift | OEE % | Labour Utilisation % | Downtime (mins) | Status |
|-------|-------|---------------------|-----------------|--------|
| Morning | 76.77% | 87.64% | 29.31 | 🟡 Warning |
| Evening | 50.71% | 83.16% | 50.82 | 🔴 Critical |
| Night | 39.07% | 79.56% | 68.12 | 🔴 Critical |

### Critical Insights
- 🔴 **Night shift** consistently ranked lowest across ALL KPIs with no Expert level operators assigned
- 🔴 **Defect Rate at 3.09%** — more than 3x the 1% industry green threshold
- 🔴 **Performance % at 64.42%** — the biggest drag on OEE
- 🔴 **Mechanical and Electrical Faults** account for over 60% of all downtime causes
- ⚠️ **Unrecorded downtime** — machines experiencing downtime with no maintenance records logged

---

## 📊 Dashboards

### 1. Performance Dashboard
> *Audience: Senior Management and Supervisors*

Tracks overall operational efficiency including OEE and its 3 components — Availability, Performance and Quality — alongside Output per Hour and Average Cycle Time.

![Performance Dashboard](dashboards/Performance_Dashboard.png)

---

### 2. Quality Control Dashboard
> *Audience: Quality Control Team and Engineers*

Monitors quality metrics including Defect Rate, First Pass Yield and Rework Rate across machines, shifts and operators — benchmarked against automotive industry standards.

![Quality Control Dashboard](dashboards/Quality_Control_Dashboard.png)

---

### 3. Maintenance Dashboard
> *Audience: Maintenance Engineers and Operations Managers*

Analyses machine downtime, issue type distribution and maintenance patterns to identify root causes and recurring failure points.

![Maintenance Dashboard](dashboards/Maintenance_Dashboard.png)

---

### 4. Operators Dashboard
> *Audience: HR, Shift Supervisors and Operations Managers*

Examines operator performance, skill distribution and productivity by experience level and shift — highlighting workforce gaps and training opportunities.

![Operators Dashboard](dashboards/Operators_Dashboard.png)

---

### Dashboard Features
- Synchronised slicers — Date, Shift and Machine across all 4 dashboards
- Traffic light conditional formatting aligned to Industry 4.0 benchmarks
- KPI cards with actual vs target and distance to goal
- Drill-through capabilities for detailed analysis

---

## 💡 Recommendations

1. **Close the OEE Performance Gap** — root cause analysis on Performance % at 64%
2. **Investigate Night Shift Underperformance** — staffing, supervision and machine allocation review
3. **Implement Mandatory Maintenance Logging** — every downtime event must trigger a maintenance record
4. **Reduce Defect Rate** — stricter QC checkpoints and machine level intervention
5. **Improve Data Integrity at Source** — automated validation rules at point of data capture
6. **Adopt Real Time Machine Monitoring** — IoT enabled monitoring in line with Industry 4.0
7. **Invest in Operator Training** — structured programme aligned to skill categories and experience levels

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| Microsoft Power BI | Data modelling, KPI calculation, dashboards |
| Power Query (M Language) | Data cleaning and transformation |
| DAX | KPI measures and calculated columns |
| SQLite3 | Source database |
| Microsoft PowerPoint | Presentation of findings |
| Microsoft Word | Project documentation |

---

## 🌍 SDG 9 Advocacy

This project aligns with **UN Sustainable Development Goal 9 — Industry, Innovation and Infrastructure**. By leveraging data analytics to drive smarter manufacturing decisions, this project demonstrates how digital tools can advance industrial resilience, close efficiency gaps and support sustainable industrialisation.

---



---

## 👤 Author

**Promise Ezeike**
Data Analyst & Consultant @ AMDARI

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/)

---

*This project was developed as part of a cross-functional analytics programme at AMDARI.*

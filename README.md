# 🩺 Apple Watch Health Analytics Dashboard

# An end-to-end automated health data visualization system built with Power BI

# 

# 📋 Executive Summary

# This project demonstrates a production-grade analytics pipeline that transforms raw Apple Health export data into actionable wellness insights. Built entirely in Power BI, the solution features automated data ingestion, star-schema modeling, and dynamic DAX measures that enable real-time health tracking across activity, cardiovascular performance, and sleep quality metrics.

# Key Achievement: Zero-touch data refresh architecture—simply drop new Apple Health exports into the source folder and refresh to update all visualizations automatically.

# 

# 🎯 Business Problem

# Personal health data from wearables like Apple Watch generates thousands of data points daily, but lacks accessible analytics for trend identification and behavior optimization. This project addresses three critical needs:

# 

# Data Accessibility – Transform Apple Health's XML/CSV exports into structured, queryable datasets

# Automated Monitoring – Enable continuous tracking without manual data processing

# Actionable Insights – Visualize correlations between activity, recovery, and sleep quality

# 

# 

# 🏗️ Architecture Overview

# ┌─────────────────────┐

# │  Apple Health App   │

# │   (Raw Export)      │

# └──────────┬──────────┘

# &nbsp;          │

# &nbsp;          ▼

# ┌─────────────────────┐

# │  Health Export CSV  │

# │  (iOS App Export)   │

# └──────────┬──────────┘

# &nbsp;          │

# &nbsp;          ▼

# ┌─────────────────────────────────────────┐

# │         Power BI Pipeline               │

# │  ┌───────────────────────────────────┐  │

# │  │  1. Folder Connector (Auto-Load)  │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  2. Power Query Transformations   │  │

# │  │     • Data Cleaning               │  │

# │  │     • Type Casting                │  │

# │  │     • Metric Parsing              │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  3. Star Schema Model             │  │

# │  │     • 4 Fact Tables               │  │

# │  │     • 3 Dimension Tables          │  │

# │  │     • Date Intelligence           │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  4. DAX Measures \& Calculations   │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  5. Interactive Dashboard         │  │

# │  │     • Overview                    │  │

# │  │     • Daily Activity              │  │

# │  │     • Heart \& Recovery            │  │

# │  │     • Sleep Insights              │  │

# │  └───────────────────────────────────┘  │

# └─────────────────────────────────────────┘

# 

# 📊 Data Engineering

# Data Sources

# Period: September–October 2025

# Extraction Method: Health Export CSV iOS app

# Update Frequency: On-demand (manual refresh)

# Record Volume: ~50,000 rows across all tables

# Data Model Design

# Implemented a star schema for optimal query performance and analytical flexibility:

# Fact Tables

# TableGranularityKey MetricsRecordsDailyMetricsDaily aggregatesSteps, Distance, Energy (Active/Basal), Stand Hours, Exercise Minutes~60SamplesTimestamp-levelHeart Rate, HRV SDNN, Respiratory Rate, SpO2~35,000IntervalsTime windowsAudio Exposure Events, Environmental Sound Levels~500Sleep\_SegmentsSleep cyclesStage Duration (Core/Deep/REM/Awake), Sleep Score~180

# Dimension Tables

# TablePurposeAttributesDimDateTime intelligenceDate, Year, Month, Weekday, IsWeekend, YearMonthDeviceDevice metadataDeviceID, Model, Name, OS VersionMetricMetric definitionsMetricID, FriendlyName, Category, Unit

# Power Query Transformations

# Key ETL operations implemented:

# 

# Header standardization – Renamed columns to consistent camelCase format

# Type casting – Converted text dates to Date type, numeric strings to decimals

# Metric parsing – Split combined metric fields (e.g., "heart\_rate 75 bpm") into separate columns

# Null handling – Replaced missing values with category-appropriate defaults

# Sleep segmentation – Calculated duration in minutes from start/end timestamps

# Cross-midnight logic – Correctly assigned sleep sessions spanning midnight to the sleep-start date

# 

# 

# 🧮 Advanced DAX Implementation

# Dynamic Date Table

# Automatically generates date range from all fact tables:

# daxDimDate =

# VAR MinDate =

# &nbsp;   MINX (

# &nbsp;       UNION (

# &nbsp;           SELECTCOLUMNS ( DailyMetrics, "D", DailyMetrics\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Samples, "D", Samples\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Intervals, "D", Intervals\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Sleep\_Segments, "D", Sleep\_Segments\[Date] )

# &nbsp;       ),

# &nbsp;       \[D]

# &nbsp;   )

# VAR MaxDate =

# &nbsp;   MAXX (

# &nbsp;       UNION (

# &nbsp;           SELECTCOLUMNS ( DailyMetrics, "D", DailyMetrics\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Samples, "D", Samples\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Intervals, "D", Intervals\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Sleep\_Segments, "D", Sleep\_Segments\[Date] )

# &nbsp;       ),

# &nbsp;       \[D]

# &nbsp;   )

# RETURN

# ADDCOLUMNS (

# &nbsp;   CALENDAR ( MinDate, MaxDate ),

# &nbsp;   "Year", YEAR ( \[Date] ),

# &nbsp;   "MonthName", FORMAT ( \[Date], "MMM" ),

# &nbsp;   "Weekday", WEEKDAY ( \[Date], 2 ),

# &nbsp;   "IsWeekend", IF ( WEEKDAY ( \[Date], 2 ) >= 6, 1, 0 ),

# &nbsp;   "YearMonth", FORMAT ( \[Date], "yyyy-MM" )

# )

# Key Calculated Measures

# dax-- Total Sleep Hours (aggregated from segments)

# Total Sleep Hours = 

# SUMX(

# &nbsp;   Sleep\_Segments,

# &nbsp;   Sleep\_Segments\[Duration\_Minutes]

# ) / 60

# 

# -- Average Daily Steps

# Avg Daily Steps = 

# AVERAGE(DailyMetrics\[Steps])

# 

# -- HRV Weekly Trend

# HRV 7-Day MA = 

# AVERAGEX(

# &nbsp;   DATESINPERIOD(

# &nbsp;       DimDate\[Date],

# &nbsp;       LASTDATE(DimDate\[Date]),

# &nbsp;       -7,

# &nbsp;       DAY

# &nbsp;   ),

# &nbsp;   \[Avg HRV SDNN]

# )

# 

# -- Sleep Deficit

# Sleep Deficit = 

# \[Total Sleep Hours] - 8

# 

# -- Active Energy Burn Rate

# Active Energy per Minute = 

# DIVIDE(

# &nbsp;   SUM(DailyMetrics\[Active\_Energy\_kcal]),

# &nbsp;   SUM(DailyMetrics\[Exercise\_Minutes]),

# &nbsp;   0

# )

# ```

# 

# \### Relationship Structure

# ```

# DimDate\[Date] ──→ DailyMetrics\[Date]

# &nbsp;             ├──→ Samples\[Date]

# &nbsp;             ├──→ Intervals\[Date]

# &nbsp;             └──→ Sleep\_Segments\[Date]

# 

# Device\[DeviceID] ──→ DailyMetrics\[DeviceID]

# &nbsp;                ├──→ Samples\[DeviceID]

# &nbsp;                ├──→ Intervals\[DeviceID]

# &nbsp;                └──→ Sleep\_Segments\[DeviceID]

# 

# Metric\[Metric] ──→ Samples\[Metric]

# &nbsp;              └──→ Intervals\[Metric]

# ```

# 

# \*\*Cardinality:\*\* All relationships are One-to-Many with single-direction cross-filtering (optimized for performance)

# 

# ---

# 

# \## 📈 Dashboard Pages

# 

# \### 🏠 1. Overview

# \*\*Purpose:\*\* Executive summary of wellness metrics

# 

# \*\*Visuals:\*\*

# \- KPI cards: Daily Steps, Total Distance, Exercise Time, Stand Hours

# \- Dual-axis line chart: Active vs. Basal Energy (stacked area)

# \- Gauge visuals: Progress toward daily activity goals

# \- Month-over-month comparison table

# 

# \*\*Insights Enabled:\*\*

# \- Quick health status assessment

# \- Energy expenditure patterns

# \- Goal achievement tracking

# 

# ---

# 

# \### 🏃 2. Daily Activity

# \*\*Purpose:\*\* Deep-dive into movement and exercise metrics

# 

# \*\*Visuals:\*\*

# \- Time series line chart: Steps with 7-day moving average

# \- Clustered column chart: Exercise Minutes by day with target line

# \- Area chart: Active vs. Basal Energy breakdown

# \- Matrix table: Daily metrics with conditional formatting

# 

# \*\*Insights Enabled:\*\*

# \- Activity consistency identification

# \- Weekend vs. weekday patterns

# \- Exercise adherence tracking

# \- Energy balance analysis

# 

# ---

# 

# \### 💓 3. Heart \& Recovery

# \*\*Purpose:\*\* Cardiovascular health and recovery monitoring

# 

# \*\*Visuals:\*\*

# \- Multi-line chart: Heart Rate (min/avg/max) with HRV overlay

# \- Scatter plot: HRV SDNN vs. Resting Heart Rate (recovery quadrant analysis)

# \- Respiratory Rate trend line with ±1 SD bands

# \- SpO2 distribution histogram

# 

# \*\*Insights Enabled:\*\*

# \- Recovery status assessment (high HRV + low RHR = optimal)

# \- Stress identification (low HRV + high RHR = overtrained/stressed)

# \- Respiratory pattern anomalies

# \- Oxygen saturation trends

# 

# \*\*Clinical Context:\*\*

# \- HRV SDNN >50ms indicates good recovery

# \- Resting HR <60 bpm suggests cardiovascular fitness

# \- Respiratory rate 12-20 breaths/min is normal range

# 

# ---

# 

# \### 🌙 4. Sleep Insights

# \*\*Purpose:\*\* Sleep quality and architecture analysis

# 

# \*\*Visuals:\*\*

# \- Stacked bar chart: Sleep stage composition (Core/Deep/REM/Awake) by night

# \- Line chart: Total sleep duration with target line (8 hours) and ±1 SD bands

# \- Sleep efficiency gauge: (Time Asleep / Time in Bed) × 100

# \- Wrist temperature deviation trend (sleep quality proxy)

# \- Matrix: Sleep metrics by weekday (identifying consistency)

# 

# \*\*Insights Enabled:\*\*

# \- Sleep debt accumulation tracking

# \- Sleep stage balance assessment (target: 50-60% Core, 20-25% Deep, 20-25% REM)

# \- Circadian rhythm consistency

# \- Temperature-based sleep quality correlation

# 

# \*\*Key Findings:\*\*

# \- Average sleep duration: \[Calculated from data]

# \- Sleep efficiency: \[Calculated from data]

# \- Most consistent sleep days: \[Identified from variance analysis]

# 

# ---

# 

# \## 🔄 Automation \& Scalability

# 

# \### Zero-Touch Refresh Process

# 

# 1\. \*\*Export new data\*\* from Apple Health using Health Export CSV app

# 2\. \*\*Drop files\*\* into the designated source folder (e.g., `C:\\HealthData\\`)

# 3\. \*\*Refresh\*\* Power BI report (Ctrl+R or scheduled refresh in Power BI Service)

# 4\. \*\*All visualizations update\*\* automatically—date ranges, measures, and filters adjust dynamically

# 

# \### Technical Implementation

# 

# \- \*\*Folder connector\*\* uses relative paths—no hardcoded file names

# \- \*\*Dynamic date table\*\* regenerates based on actual data range

# \- \*\*Parameterized queries\*\* allow easy source folder path updates

# \- \*\*Incremental refresh-ready\*\* architecture (can be configured for Power BI Service)

# 

# ---

# 

# \## 🔍 Key Insights \& Findings

# 

# \### Activity Patterns

# \- \*\*Peak activity days:\*\* \[Weekends/Weekdays based on data]

# \- \*\*Average daily steps:\*\* \[Calculated metric]

# \- \*\*Exercise consistency:\*\* \[% of days meeting 30min target]

# 

# \### Recovery Metrics

# \- \*\*Average HRV:\*\* \[Mean SDNN value] ms (trend: improving/stable/declining)

# \- \*\*Resting heart rate:\*\* \[Mean RHR] bpm

# \- \*\*Recovery score correlation:\*\* HRV inversely correlated with RHR (r = \[calculated])

# 

# \### Sleep Quality

# \- \*\*Sleep debt:\*\* \[Total hours below 8hr target over period]

# \- \*\*Most restorative sleep stage:\*\* \[Deep/REM percentage analysis]

# \- \*\*Temperature-sleep correlation:\*\* Wrist temperature drops of \[X]°C associated with better sleep efficiency

# 

# ---

# 

# \## 🛠️ Technical Stack

# 

# | Component | Technology | Purpose |

# |-----------|-----------|---------|

# | \*\*Data Extraction\*\* | Health Export CSV (iOS) | Apple Health data export |

# | \*\*ETL\*\* | Power Query (M language) | Data cleaning and transformation |

# | \*\*Data Modeling\*\* | Power BI Data Model | Star schema implementation |

# | \*\*Calculations\*\* | DAX (Data Analysis Expressions) | Custom measures and time intelligence |

# | \*\*Visualization\*\* | Power BI Desktop | Interactive dashboard creation |

# | \*\*Version Control\*\* | Git / GitHub | Project documentation and code management |

# 

# ---

# 

# \## 📂 Repository Structure

# ```

# 📁 AppleWatch\_Health\_Analytics/

# │

# ├── 📁 assets/

# │   ├── PowerQueryOverview.png

# │   ├── DataModel\_StarSchema.png

# │   ├── DailyMetrics\_Table.png

# │   ├── Samples\_Table.png

# │   ├── Intervals\_Table.png

# │   ├── Metric\_Table.png

# │   ├── Dashboard\_Overview.png

# │   ├── Dashboard\_DailyActivity.png

# │   ├── Dashboard\_HeartRecovery.png

# │   └── Dashboard\_SleepInsights.png

# │

# ├── 📁 documentation/

# │   ├── DAX\_Measures.md

# │   ├── PowerQuery\_Transformations.md

# │   └── Data\_Dictionary.md

# │

# ├── AppleWatch\_Health\_Dashboard.pbix

# ├── README.md

# └── LICENSE

# 

# 🚀 Future Enhancements

# Phase 2: Advanced Analytics

# 

# &nbsp;Predictive modeling – Forecast sleep quality using previous day's HRV and activity

# &nbsp;Anomaly detection – Flag unusual heart rate patterns or activity drops

# &nbsp;Correlation analysis – Statistical testing of activity-sleep-recovery relationships

# 

# Phase 3: Extended Metrics

# 

# &nbsp;VO₂ Max tracking – Cardio fitness trends

# &nbsp;Nutrition integration – Calorie intake vs. expenditure

# &nbsp;Workout segmentation – Detailed exercise type analysis (running, strength, yoga)

# &nbsp;Stress scores – Combine HRV, RHR, and sleep quality into composite metric

# 

# Phase 4: Intelligence Layer

# 

# &nbsp;Power BI Copilot integration – Natural language insights

# &nbsp;Automated reporting – Weekly health summary emails

# &nbsp;Goal recommendations – ML-driven personalized targets

# 

# 

# 📊 Performance Metrics

# Dashboard Performance:

# 

# Report size: ~8MB (compressed)

# Average refresh time: <30 seconds

# Visual render time: <2 seconds

# Dataset rows: ~50,000 across all tables

# 

# Optimization Techniques Applied:

# 

# Star schema design for minimal query overhead

# Aggregated fact tables for daily metrics

# Bidirectional filtering disabled where not required

# Calculated columns minimized (prefer measures)

# 

# 

# 🎓 Learning Outcomes

# This project demonstrates proficiency in:

# 

# Data Engineering

# 

# Building automated ETL pipelines

# Designing dimensional models

# Implementing data quality checks

# 

# 

# Business Intelligence

# 

# Creating interactive dashboards

# Developing DAX measures for complex calculations

# Designing user-centric visualizations

# 

# 

# Domain Knowledge

# 

# Understanding health metrics and clinical ranges

# Interpreting biometric data patterns

# Translating analytics into actionable wellness insights

# 

# 

# Technical Skills

# 

# Power Query (M language)

# DAX (Data Analysis Expressions)

# Power BI Desktop \& Service

# Git version control

# 

# 

# 

# 

# 🤝 Contributing

# Interested in extending this project? Areas for contribution:

# 

# Data sources: Integration with Fitbit, Garmin, or Oura Ring data

# Visualizations: Additional chart types or dashboard pages

# Calculations: New health metrics or statistical analyses

# Documentation: Expanded data dictionary or user guides

# 

# 

# 📧 Contact

# Yajurved Jayavarapu

# Data Scientist 

# 📧 \[yjayavarapu@gmail.com

# 💼 \[https://www.linkedin.com/in/yajurved-jayavarapu/]

# 📂 \[https://github.com/yajurved987]

# 

# 📄 License

# This project is licensed under the MIT License - see the LICENSE file for details.

# 

# 🙏 Acknowledgments

# 

# Apple Health for comprehensive personal health data tracking

# Health Export CSV app developers for simplified data extraction

# Power BI Community for DAX optimization techniques and best practices

# 

# 

# 💡 Project Philosophy

# 

# "Data without action is just noise. This dashboard transforms wearable data into behavioral insights—because the real value isn't in the numbers, it's in the healthier decisions they inspire."

# 

# 

# ⭐ If you found this project helpful, please consider starring the repository!🩺 Apple Watch Health Analytics Dashboard

# An end-to-end automated health data visualization system built with Power BI

# 

# 📋 Executive Summary

# This project demonstrates a production-grade analytics pipeline that transforms raw Apple Health export data into actionable wellness insights. Built entirely in Power BI, the solution features automated data ingestion, star-schema modeling, and dynamic DAX measures that enable real-time health tracking across activity, cardiovascular performance, and sleep quality metrics.

# Key Achievement: Zero-touch data refresh architecture—simply drop new Apple Health exports into the source folder and refresh to update all visualizations automatically.

# 

# 🎯 Business Problem

# Personal health data from wearables like Apple Watch generates thousands of data points daily, but lacks accessible analytics for trend identification and behavior optimization. This project addresses three critical needs:

# 

# Data Accessibility – Transform Apple Health's XML/CSV exports into structured, queryable datasets

# Automated Monitoring – Enable continuous tracking without manual data processing

# Actionable Insights – Visualize correlations between activity, recovery, and sleep quality

# 

# 

# 🏗️ Architecture Overview

# ┌─────────────────────┐

# │  Apple Health App   │

# │   (Raw Export)      │

# └──────────┬──────────┘

# &nbsp;          │

# &nbsp;          ▼

# ┌─────────────────────┐

# │  Health Export CSV  │

# │  (iOS App Export)   │

# └──────────┬──────────┘

# &nbsp;          │

# &nbsp;          ▼

# ┌─────────────────────────────────────────┐

# │         Power BI Pipeline               │

# │  ┌───────────────────────────────────┐  │

# │  │  1. Folder Connector (Auto-Load)  │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  2. Power Query Transformations   │  │

# │  │     • Data Cleaning               │  │

# │  │     • Type Casting                │  │

# │  │     • Metric Parsing              │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  3. Star Schema Model             │  │

# │  │     • 4 Fact Tables               │  │

# │  │     • 3 Dimension Tables          │  │

# │  │     • Date Intelligence           │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  4. DAX Measures \& Calculations   │  │

# │  └───────────────┬───────────────────┘  │

# │                  │                       │

# │  ┌───────────────▼───────────────────┐  │

# │  │  5. Interactive Dashboard         │  │

# │  │     • Overview                    │  │

# │  │     • Daily Activity              │  │

# │  │     • Heart \& Recovery            │  │

# │  │     • Sleep Insights              │  │

# │  └───────────────────────────────────┘  │

# └─────────────────────────────────────────┘

# 

# 📊 Data Engineering

# Data Sources

# Period: September–October 2025

# Extraction Method: Health Export CSV iOS app

# Update Frequency: On-demand (manual refresh)

# Record Volume: ~50,000 rows across all tables

# Data Model Design

# Implemented a star schema for optimal query performance and analytical flexibility:

# Fact Tables

# TableGranularityKey MetricsRecordsDailyMetricsDaily aggregatesSteps, Distance, Energy (Active/Basal), Stand Hours, Exercise Minutes~60SamplesTimestamp-levelHeart Rate, HRV SDNN, Respiratory Rate, SpO2~35,000IntervalsTime windowsAudio Exposure Events, Environmental Sound Levels~500Sleep\_SegmentsSleep cyclesStage Duration (Core/Deep/REM/Awake), Sleep Score~180

# Dimension Tables

# TablePurposeAttributesDimDateTime intelligenceDate, Year, Month, Weekday, IsWeekend, YearMonthDeviceDevice metadataDeviceID, Model, Name, OS VersionMetricMetric definitionsMetricID, FriendlyName, Category, Unit

# Power Query Transformations

# Key ETL operations implemented:

# 

# Header standardization – Renamed columns to consistent camelCase format

# Type casting – Converted text dates to Date type, numeric strings to decimals

# Metric parsing – Split combined metric fields (e.g., "heart\_rate 75 bpm") into separate columns

# Null handling – Replaced missing values with category-appropriate defaults

# Sleep segmentation – Calculated duration in minutes from start/end timestamps

# Cross-midnight logic – Correctly assigned sleep sessions spanning midnight to the sleep-start date

# 

# 

# 🧮 Advanced DAX Implementation

# Dynamic Date Table

# Automatically generates date range from all fact tables:

# daxDimDate =

# VAR MinDate =

# &nbsp;   MINX (

# &nbsp;       UNION (

# &nbsp;           SELECTCOLUMNS ( DailyMetrics, "D", DailyMetrics\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Samples, "D", Samples\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Intervals, "D", Intervals\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Sleep\_Segments, "D", Sleep\_Segments\[Date] )

# &nbsp;       ),

# &nbsp;       \[D]

# &nbsp;   )

# VAR MaxDate =

# &nbsp;   MAXX (

# &nbsp;       UNION (

# &nbsp;           SELECTCOLUMNS ( DailyMetrics, "D", DailyMetrics\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Samples, "D", Samples\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Intervals, "D", Intervals\[Date] ),

# &nbsp;           SELECTCOLUMNS ( Sleep\_Segments, "D", Sleep\_Segments\[Date] )

# &nbsp;       ),

# &nbsp;       \[D]

# &nbsp;   )

# RETURN

# ADDCOLUMNS (

# &nbsp;   CALENDAR ( MinDate, MaxDate ),

# &nbsp;   "Year", YEAR ( \[Date] ),

# &nbsp;   "MonthName", FORMAT ( \[Date], "MMM" ),

# &nbsp;   "Weekday", WEEKDAY ( \[Date], 2 ),

# &nbsp;   "IsWeekend", IF ( WEEKDAY ( \[Date], 2 ) >= 6, 1, 0 ),

# &nbsp;   "YearMonth", FORMAT ( \[Date], "yyyy-MM" )

# )

# Key Calculated Measures

# dax-- Total Sleep Hours (aggregated from segments)

# Total Sleep Hours = 

# SUMX(

# &nbsp;   Sleep\_Segments,

# &nbsp;   Sleep\_Segments\[Duration\_Minutes]

# ) / 60

# 

# -- Average Daily Steps

# Avg Daily Steps = 

# AVERAGE(DailyMetrics\[Steps])

# 

# -- HRV Weekly Trend

# HRV 7-Day MA = 

# AVERAGEX(

# &nbsp;   DATESINPERIOD(

# &nbsp;       DimDate\[Date],

# &nbsp;       LASTDATE(DimDate\[Date]),

# &nbsp;       -7,

# &nbsp;       DAY

# &nbsp;   ),

# &nbsp;   \[Avg HRV SDNN]

# )

# 

# -- Sleep Deficit

# Sleep Deficit = 

# \[Total Sleep Hours] - 8

# 

# -- Active Energy Burn Rate

# Active Energy per Minute = 

# DIVIDE(

# &nbsp;   SUM(DailyMetrics\[Active\_Energy\_kcal]),

# &nbsp;   SUM(DailyMetrics\[Exercise\_Minutes]),

# &nbsp;   0

# )

# ```

# 

# \### Relationship Structure

# ```

# DimDate\[Date] ──→ DailyMetrics\[Date]

# &nbsp;             ├──→ Samples\[Date]

# &nbsp;             ├──→ Intervals\[Date]

# &nbsp;             └──→ Sleep\_Segments\[Date]

# 

# Device\[DeviceID] ──→ DailyMetrics\[DeviceID]

# &nbsp;                ├──→ Samples\[DeviceID]

# &nbsp;                ├──→ Intervals\[DeviceID]

# &nbsp;                └──→ Sleep\_Segments\[DeviceID]

# 

# Metric\[Metric] ──→ Samples\[Metric]

# &nbsp;              └──→ Intervals\[Metric]

# ```

# 

# \*\*Cardinality:\*\* All relationships are One-to-Many with single-direction cross-filtering (optimized for performance)

# 

# ---

# 

# \## 📈 Dashboard Pages

# 

# \### 🏠 1. Overview

# \*\*Purpose:\*\* Executive summary of wellness metrics

# 

# \*\*Visuals:\*\*

# \- KPI cards: Daily Steps, Total Distance, Exercise Time, Stand Hours

# \- Dual-axis line chart: Active vs. Basal Energy (stacked area)

# \- Gauge visuals: Progress toward daily activity goals

# \- Month-over-month comparison table

# 

# \*\*Insights Enabled:\*\*

# \- Quick health status assessment

# \- Energy expenditure patterns

# \- Goal achievement tracking

# 

# ---

# 

# \### 🏃 2. Daily Activity

# \*\*Purpose:\*\* Deep-dive into movement and exercise metrics

# 

# \*\*Visuals:\*\*

# \- Time series line chart: Steps with 7-day moving average

# \- Clustered column chart: Exercise Minutes by day with target line

# \- Area chart: Active vs. Basal Energy breakdown

# \- Matrix table: Daily metrics with conditional formatting

# 

# \*\*Insights Enabled:\*\*

# \- Activity consistency identification

# \- Weekend vs. weekday patterns

# \- Exercise adherence tracking

# \- Energy balance analysis

# 

# ---

# 

# \### 💓 3. Heart \& Recovery

# \*\*Purpose:\*\* Cardiovascular health and recovery monitoring

# 

# \*\*Visuals:\*\*

# \- Multi-line chart: Heart Rate (min/avg/max) with HRV overlay

# \- Scatter plot: HRV SDNN vs. Resting Heart Rate (recovery quadrant analysis)

# \- Respiratory Rate trend line with ±1 SD bands

# \- SpO2 distribution histogram

# 

# \*\*Insights Enabled:\*\*

# \- Recovery status assessment (high HRV + low RHR = optimal)

# \- Stress identification (low HRV + high RHR = overtrained/stressed)

# \- Respiratory pattern anomalies

# \- Oxygen saturation trends

# 

# \*\*Clinical Context:\*\*

# \- HRV SDNN >50ms indicates good recovery

# \- Resting HR <60 bpm suggests cardiovascular fitness

# \- Respiratory rate 12-20 breaths/min is normal range

# 

# ---

# 

# \### 🌙 4. Sleep Insights

# \*\*Purpose:\*\* Sleep quality and architecture analysis

# 

# \*\*Visuals:\*\*

# \- Stacked bar chart: Sleep stage composition (Core/Deep/REM/Awake) by night

# \- Line chart: Total sleep duration with target line (8 hours) and ±1 SD bands

# \- Sleep efficiency gauge: (Time Asleep / Time in Bed) × 100

# \- Wrist temperature deviation trend (sleep quality proxy)

# \- Matrix: Sleep metrics by weekday (identifying consistency)

# 

# \*\*Insights Enabled:\*\*

# \- Sleep debt accumulation tracking

# \- Sleep stage balance assessment (target: 50-60% Core, 20-25% Deep, 20-25% REM)

# \- Circadian rhythm consistency

# \- Temperature-based sleep quality correlation

# 

# \*\*Key Findings:\*\*

# \- Average sleep duration: \[Calculated from data]

# \- Sleep efficiency: \[Calculated from data]

# \- Most consistent sleep days: \[Identified from variance analysis]

# 

# ---

# 

# \## 🔄 Automation \& Scalability

# 

# \### Zero-Touch Refresh Process

# 

# 1\. \*\*Export new data\*\* from Apple Health using Health Export CSV app

# 2\. \*\*Drop files\*\* into the designated source folder (e.g., `C:\\HealthData\\`)

# 3\. \*\*Refresh\*\* Power BI report (Ctrl+R or scheduled refresh in Power BI Service)

# 4\. \*\*All visualizations update\*\* automatically—date ranges, measures, and filters adjust dynamically

# 

# \### Technical Implementation

# 

# \- \*\*Folder connector\*\* uses relative paths—no hardcoded file names

# \- \*\*Dynamic date table\*\* regenerates based on actual data range

# \- \*\*Parameterized queries\*\* allow easy source folder path updates

# \- \*\*Incremental refresh-ready\*\* architecture (can be configured for Power BI Service)

# 

# ---

# 

# \## 🔍 Key Insights \& Findings

# 

# \### Activity Patterns

# \- \*\*Peak activity days:\*\* \[Weekends/Weekdays based on data]

# \- \*\*Average daily steps:\*\* \[Calculated metric]

# \- \*\*Exercise consistency:\*\* \[% of days meeting 30min target]

# 

# \### Recovery Metrics

# \- \*\*Average HRV:\*\* \[Mean SDNN value] ms (trend: improving/stable/declining)

# \- \*\*Resting heart rate:\*\* \[Mean RHR] bpm

# \- \*\*Recovery score correlation:\*\* HRV inversely correlated with RHR (r = \[calculated])

# 

# \### Sleep Quality

# \- \*\*Sleep debt:\*\* \[Total hours below 8hr target over period]

# \- \*\*Most restorative sleep stage:\*\* \[Deep/REM percentage analysis]

# \- \*\*Temperature-sleep correlation:\*\* Wrist temperature drops of \[X]°C associated with better sleep efficiency

# 

# ---

# 

# \## 🛠️ Technical Stack

# 

# | Component | Technology | Purpose |

# |-----------|-----------|---------|

# | \*\*Data Extraction\*\* | Health Export CSV (iOS) | Apple Health data export |

# | \*\*ETL\*\* | Power Query (M language) | Data cleaning and transformation |

# | \*\*Data Modeling\*\* | Power BI Data Model | Star schema implementation |

# | \*\*Calculations\*\* | DAX (Data Analysis Expressions) | Custom measures and time intelligence |

# | \*\*Visualization\*\* | Power BI Desktop | Interactive dashboard creation |

# | \*\*Version Control\*\* | Git / GitHub | Project documentation and code management |

# 

# ---

# 

# \## 📂 Repository Structure

# ```

# 📁 AppleWatch\_Health\_Analytics/

# │

# ├── 📁 assets/

# │   ├── PowerQueryOverview.png

# │   ├── DataModel\_StarSchema.png

# │   ├── DailyMetrics\_Table.png

# │   ├── Samples\_Table.png

# │   ├── Intervals\_Table.png

# │   ├── Metric\_Table.png

# │   ├── Dashboard\_Overview.png

# │   ├── Dashboard\_DailyActivity.png

# │   ├── Dashboard\_HeartRecovery.png

# │   └── Dashboard\_SleepInsights.png

# │

# ├── 📁 documentation/

# │   ├── DAX\_Measures.md

# │   ├── PowerQuery\_Transformations.md

# │   └── Data\_Dictionary.md

# │

# ├── AppleWatch\_Health\_Dashboard.pbix

# ├── README.md

# └── LICENSE

# 

# 🚀 Future Enhancements

# Phase 2: Advanced Analytics

# 

# &nbsp;Predictive modeling – Forecast sleep quality using previous day's HRV and activity

# &nbsp;Anomaly detection – Flag unusual heart rate patterns or activity drops

# &nbsp;Correlation analysis – Statistical testing of activity-sleep-recovery relationships

# 

# Phase 3: Extended Metrics

# 

# &nbsp;VO₂ Max tracking – Cardio fitness trends

# &nbsp;Nutrition integration – Calorie intake vs. expenditure

# &nbsp;Workout segmentation – Detailed exercise type analysis (running, strength, yoga)

# &nbsp;Stress scores – Combine HRV, RHR, and sleep quality into composite metric

# 

# Phase 4: Intelligence Layer

# 

# &nbsp;Power BI Copilot integration – Natural language insights

# &nbsp;Automated reporting – Weekly health summary emails

# &nbsp;Goal recommendations – ML-driven personalized targets

# 

# 

# 📊 Performance Metrics

# Dashboard Performance:

# 

# Report size: ~8MB (compressed)

# Average refresh time: <30 seconds

# Visual render time: <2 seconds

# Dataset rows: ~50,000 across all tables

# 

# Optimization Techniques Applied:

# 

# Star schema design for minimal query overhead

# Aggregated fact tables for daily metrics

# Bidirectional filtering disabled where not required

# Calculated columns minimized (prefer measures)

# 

# 

# 🎓 Learning Outcomes

# This project demonstrates proficiency in:

# 

# Data Engineering

# 

# Building automated ETL pipelines

# Designing dimensional models

# Implementing data quality checks

# 

# 

# Business Intelligence

# 

# Creating interactive dashboards

# Developing DAX measures for complex calculations

# Designing user-centric visualizations

# 

# 

# Domain Knowledge

# 

# Understanding health metrics and clinical ranges

# Interpreting biometric data patterns

# Translating analytics into actionable wellness insights

# 

# 

# Technical Skills

# 

# Power Query (M language)

# DAX (Data Analysis Expressions)

# Power BI Desktop \& Service

# Git version control

# 

# 

# 

# 

# 🤝 Contributing

# Interested in extending this project? Areas for contribution:

# 

# Data sources: Integration with Fitbit, Garmin, or Oura Ring data

# Visualizations: Additional chart types or dashboard pages

# Calculations: New health metrics or statistical analyses

# Documentation: Expanded data dictionary or user guides

# 

# 

# 📧 Contact

# Yajurved Jayavarapu

# Data Scientist 

# 📧 \[yjayavarapu@gmail.com

# 💼 \[https://www.linkedin.com/in/yajurved-jayavarapu/]

# 📂 \[https://github.com/yajurved987]

# 

# 📄 License

# This project is licensed under the MIT License - see the LICENSE file for details.

# 

# 🙏 Acknowledgments

# 

# Apple Health for comprehensive personal health data tracking

# Health Export CSV app developers for simplified data extraction

# Power BI Community for DAX optimization techniques and best practices

# 

# 

# 💡 Project Philosophy

# 

# "Data without action is just noise. This dashboard transforms wearable data into behavioral insights—because the real value isn't in the numbers, it's in the healthier decisions they inspire."

# 

# 

# ⭐ If you found this project helpful, please consider starring the repository!


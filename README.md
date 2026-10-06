<div align="center">

# 🌦️ Weather Data Analysis & Interactive Dashboard

**Validating nearly 10 years of daily weather records, engineering climate features, and turning them into KPIs and an interactive dashboard**

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Cleaning-150458?logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-Validation-013243?logo=numpy&logoColor=white)
![Dashboard](https://img.shields.io/badge/Dashboard-Power%20BI%20%2F%20Tableau-F2C811)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

</div>

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Business Questions & Answers](#-business-questions--answers)
- [Dataset](#-dataset)
- [Tech Stack](#-tech-stack)
- [Workflow](#-workflow)
- [Data Quality Report](#-data-quality-report)
- [Feature Engineering](#-feature-engineering)
- [Dashboard](#-dashboard)
- [Key Insights](#-key-insights)
- [Repository Structure](#-repository-structure)
- [How to Run](#-how-to-run)
- [Author](#-author)

---

## 🎯 Project Overview

A weather data platform wants to understand climate patterns from historical observations: how **temperature, humidity, wind, and rainfall** behave across time, and where unusual weather appears.

This project takes **3,271 daily weather records (Feb 2008 to Jun 2017)** through a full analytics pipeline:

**validate → clean → engineer features → define KPIs → interactive dashboard → insights**

Key principle: **real weather is never deleted or invented.** Extreme events (like heatwaves and heavy rain) are flagged rather than removed, and missing dates are flagged rather than interpolated.

## ❓ Business Questions & Answers

| # | Question | Answer |
|---|---|---|
| 1 | **Is the dataset complete and consistent?** | Yes. **0 missing values**, **0 duplicate rows**, and **0 logic violations** (no MinTemp > MaxTemp, no humidity outside 0 to 100%, no negative wind speeds, no 3pm temperature above the daily max) |
| 2 | **Are there gaps in the timeline?** | Yes. The period spans 3,433 days but only 3,271 are recorded, so **162 days (4.7%) are missing** across **45 gaps**. They were **flagged, not interpolated**, so averages use only available days |
| 3 | **Are there extreme weather events?** | **61 days (1.9%)** were flagged as extreme: max temperature ≥ 40°C, rainfall ≥ 50 mm, or wind gusts ≥ 80 km/h. All were **kept**, since they are real events |
| 4 | **Does `RainToday` match the rainfall readings?** | Almost. Against a "≥ 1.0 mm = rain" rule there are **37 mismatches, all at exactly 1.0 mm**, which suggests the source flags rain only above 1 mm |
| 5 | **Can `RainTomorrow` be trusted?** | Yes. It matches the next day's `RainToday` on **100% of the 3,225 consecutive-day pairs** |
| 6 | **How was weather severity categorized?** | Rainfall into *None / Light / Moderate / Heavy / Extreme*, wind gusts into *Light / Moderate / Strong / Gale*, and wind direction into *North / East / South / West* quadrants |
| 7 | **Which KPIs matter most?** | Average temperature, temperature range, heat days (≥ 35°C), rain days, total and average rainfall, humidity, and wind gust speed, all sliceable by year, quarter, month, and season |
| 8 | **How do temperature, rainfall, humidity, and wind behave over time?** | See [Key Insights](#-key-insights) and the dashboard |

## 🗂 Dataset

| Property | Details |
|---|---|
| **File** | `Weather_Data.csv` |
| **Rows × Columns** | 3,271 × 22 raw |
| **Period** | 1 Feb 2008 to 25 Jun 2017 (daily) |
| **Hemisphere** | Southern (seasons mapped accordingly) |

**Fields:** `Date`, `MinTemp`, `MaxTemp`, `Rainfall`, `Evaporation`, `Sunshine`, `WindGustDir`, `WindGustSpeed`, `WindDir9am`, `WindDir3pm`, `WindSpeed9am`, `WindSpeed3pm`, `Humidity9am`, `Humidity3pm`, `Pressure9am`, `Pressure3pm`, `Cloud9am`, `Cloud3pm`, `Temp9am`, `Temp3pm`, `RainToday`, `RainTomorrow`

## 🛠 Tech Stack

| Purpose | Tools |
|---|---|
| Data cleaning & validation | Python, Pandas, NumPy |
| Environment | Jupyter Notebook |
| Dashboard | `[Power BI / Tableau, keep the one you used]` |

## 🔄 Workflow

```
Raw Data → EDA → Validation → Cleaning & Flagging → Feature Engineering → KPIs → Dashboard → Insights
```

1. **EDA:** inspected structure, types, null counts, and duplicates
2. **Date handling:** converted dates to `datetime` and sorted chronologically
3. **Validation:** ran domain checks on temperature, humidity, and wind, and cross-checked the rain indicators
4. **Flagging:** marked date gaps and extreme-weather days without altering the data
5. **Feature engineering:** built time, season, temperature, rainfall, and wind features that power dashboard slicers
6. **Dashboard:** built interactive visuals and KPIs on the cleaned dataset
7. **Insights:** summarized climate patterns and data-quality findings

## 🧹 Data Quality Report

| # | Check / Issue | Finding | How It Was Handled |
|---|---|---|---|
| 1 | **Date stored as text** | `Date` was `object` type | Converted to `datetime` (`%m/%d/%Y`) and sorted |
| 2 | **Missing values** | 0 across all 22 columns | No imputation needed |
| 3 | **Duplicate rows** | 0 | No action needed |
| 4 | **Temperature logic** | 0 rows with MinTemp > MaxTemp or Temp3pm > MaxTemp | ✅ Pass |
| 5 | **Humidity bounds** | 0 values outside 0 to 100% | ✅ Pass |
| 6 | **Wind sanity** | 0 negative wind speeds | ✅ Pass |
| 7 | **`RainToday` vs `Rainfall`** | 37 mismatches, all at exactly 1.0 mm | Documented. The source appears to flag rain only above 1 mm |
| 8 | **`RainTomorrow` integrity** | 100% match with next day's `RainToday` (3,225 pairs) | ✅ Pass |
| 9 | **Missing calendar days** | 162 days missing in 45 gaps | Flagged with `Is_After_Gap`. **Not interpolated**, because weather can't be safely invented |
| 10 | **Extreme values** | 61 days with ≥ 40°C, ≥ 50 mm rain, or ≥ 80 km/h gusts | Flagged with `is_outlier`. **Kept**, since they are genuine extreme events |

## 🧩 Feature Engineering

| Feature | Logic |
|---|---|
| `Year`, `Quarter`, `Month`, `MonthName`, `YearMonth` | Extracted from `Date` for time slicers |
| `Season` | Southern Hemisphere mapping: Dec to Feb = Summer, Mar to May = Autumn, Jun to Aug = Winter, Sep to Nov = Spring |
| `AvgTemp` | `(MinTemp + MaxTemp) / 2` |
| `TempRange` | `MaxTemp − MinTemp` |
| `HeatDay` | `1` when `MaxTemp ≥ 35°C` |
| `RainDay_1mm` | `1` when `Rainfall ≥ 1.0 mm` |
| `RainCategory` | None (0), Light (< 2.5 mm), Moderate (< 10 mm), Heavy (up to 50 mm), Extreme (> 50 mm) |
| `WindCategory` | Light (< 30), Moderate (30 to 45), Strong (45 to 60), Gale (> 60 km/h) on gust speed |
| `WindGustDir_Quadrant` | 16 compass points grouped into North / East / South / West |
| `Is_After_Gap`, `Date_Diff_Days` | Flags a record that follows a missing-date gap |
| `is_outlier` | Flags extreme-weather days |


**KPIs:**
- 🌡 Average / Max / Min Temperature
- 🔥 Heat Days (≥ 35°C)
- 🌧 Rain Days and Total Rainfall
- 💧 Average Humidity
- 💨 Average Wind Gust Speed
- ⚠️ Extreme-weather days

## 💡 Key Insights

**Data reliability**
1. **The data is clean and internally consistent.** There are no nulls, duplicates, or impossible values, and `RainTomorrow` is perfectly aligned with the following day's `RainToday`.
2. **4.7% of calendar days are missing.** The 45 gaps are flagged, so any monthly or yearly averages should be read as "based on available days".
3. **1.9% of days are extreme.** These real events (heat above 40°C, rain above 50 mm, or gusts above 80 km/h) are preserved for analysis rather than removed as "outliers".

**Climate patterns** *(fill in from your dashboard)*

4. **Temperature:** `[e.g. warmest month, coldest month, number of heat days per year]`
5. **Rainfall:** `[e.g. wettest month or season, share of rain days, heaviest single day]`
6. **Humidity & wind:** `[e.g. how humidity differs between 9am and 3pm, dominant wind direction, windiest season]`
7. **Unusual patterns:** `[e.g. years with more extreme days, any clear trend over 2008 to 2017]`

## 📁 Repository Structure

```
weather_data_analysis/
│
├── Data_Files/
│   ├── Raw_Files/
│   │   └── Weather_Data.csv        # Raw dataset
│   └── Cleaned_Files/
│       └── cleaned_data.csv        # Cleaned + engineered dataset
│
├── Task6.ipynb                     # Validation, cleaning & feature engineering
├── images/
│   └── dashboard.png               # Dashboard screenshot
└── README.md
```

> Adjust the tree to match your actual repository layout.

## 🚀 How to Run

```bash
# 1. Clone the repository
git clone https://github.com/kirlosmagdy/weather_data_analysis.git
cd weather_data_analysis

# 2. Install dependencies
pip install pandas numpy jupyter

# 3. Open the notebook
jupyter notebook Task6.ipynb
```

> ⚠️ Update the file paths in the notebook (`pd.read_csv(...)` and `df.to_csv(...)`) to match your local folders. Then open `Data_Files/Cleaned_Files/cleaned_data.csv` in your dashboard tool.

## 👤 Author

**Kirolos Magdy**: Data Engineer | Analytics Engineer
Faculty of Computers and Data Science, Alexandria University

[![GitHub](https://img.shields.io/badge/GitHub-kirlosmagdy-181717?logo=github)](https://github.com/kirlosmagdy)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Kirolos%20Magdy-0A66C2?logo=linkedin)](https://linkedin.com/in/kirolos-magdy1/)

---

<div align="center">

⭐ If you found this project useful, consider giving it a star!

</div>

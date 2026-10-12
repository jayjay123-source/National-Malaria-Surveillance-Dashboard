# Malaria Incidence Trends in Nigeria (2015–2025)

### 3MTT Capstone Project (Data Analytics and Visualization Track)

I built this Power BI dashboard as my capstone project for the 3MTT Data Analytics and Visualization Track.

The project looks at how malaria cases can be analysed across Nigeria's 36 states and the FCT over time. I focused on yearly trends, regional differences, seasonal patterns, incidence, and mortality.

**Tools:** Power BI, Power Query, DAX, Microsoft Excel

---

## Dashboard Preview

### Executive Summary

This page gives an overview of the dataset, including total cases, total deaths, death rate, yearly trends, regional distribution, risk levels, monthly seasonality, and year-over-year growth.

![Executive Summary](screenshots/01-executive-summary.png)

> **Please note:** The dashboard uses simulated data, not official Nigerian malaria statistics.

---

## About the Project

I wanted to build a dashboard that could be used to explore malaria patterns across different parts of Nigeria and over several years.

I could not get state-level monthly malaria data in a suitable format for this project, so I created my own synthetic dataset. I then used Power Query, data modelling, and DAX to prepare the data and build the dashboard.

The figures are simulated, but the data preparation, calculations, validation, and dashboard development are the work carried out for this project.

## Problem Statement

How can malaria incidence be monitored across Nigerian states over time, and how can seasonal and regional patterns be identified from the data?

## Project Objectives

The dashboard was designed to help answer these questions:

- Which states have the highest simulated malaria case volumes?
- Which geopolitical regions account for the largest share of cases?
- How do cases change from year to year?
- What seasonal patterns appear in the dataset?
- How does population size relate to malaria incidence?
- Which states have higher simulated death rates?

## Dataset Overview

| Metric | Value |
|---|---:|
| Total malaria cases | 54,975,795 |
| Total deaths | 206,930 |
| Death rate (deaths ÷ cases) | 0.38% |
| Period covered | 2015–2025 |
| Locations | 36 states + FCT |
| Number of months | 132 |
| Total records | 4,884 |

### Data Grain

Each record represents one state for one month in one year.

With 37 locations covered over 132 months, the dataset contains 4,884 records.

## Data Preparation and Modelling

I used Power Query to prepare the data for analysis and organised the tables into a simple star schema.

The model contains three main tables:

### Fact_Malaria

Contains the monthly records used to analyse malaria cases, deaths, population, incidence, and risk levels.

### Dim_State

Contains state information, including region, base population, growth rate, and burden classification.

### Dim_Calendar

Contains the date information used to analyse yearly, monthly, year-to-date, and other time-based trends.

I marked the calendar table as a Date Table to support the DAX time-intelligence calculations.

The model follows this structure:

```text
             Dim_Calendar
                  |
                  |
Dim_State ---- Fact_Malaria
```

## How I Created the Synthetic Dataset

I created the dataset using a set of assumptions so I could explore different patterns in the dashboard.

**Population:** I estimated population changes using each state's 2015 base population and assigned growth rate.

**Malaria cases:** I used population, a base incidence rate, burden factors, seasonal factors, and controlled random variation to generate the case values.

**Seasonality:** I applied higher seasonal weights from May to October to represent the rainy-season pattern used in the simulation.

**Deaths:** I derived deaths from case numbers using mortality assumptions that varied according to the assigned burden.

**Incidence:** Incidence per 1,000 was calculated as cases divided by population, multiplied by 1,000.

**Risk levels:** I grouped records into Low, Moderate, High, and Very High categories using thresholds I defined for this project.

These are simulation assumptions. The risk categories are not official classifications, and the generated figures should not be treated as real health statistics.

## DAX and Time Intelligence

I created DAX measures to calculate the main dashboard metrics and compare results across different periods.

### Core measures

- Total Malaria Cases
- Total Deaths
- Death Rate
- Average Incidence

### Time-based measures

- Previous Year Cases
- Year-over-Year Cases Change
- Year-over-Year Growth %
- Year-to-Date Cases
- Previous Year YTD Cases
- YTD YoY Growth %
- Previous Month Cases
- Month-over-Month Cases Change
- Month-over-Month Growth %
- Rolling 12-Month Analysis

I also checked the calculations against yearly totals and previous-period values.

For example, December's YTD total should match the full-year total, while the 2015 year-over-year calculation should be blank because the dataset starts in 2015.

My final checks showed:

- **2024 cases:** 5,599,344
- **2025 cases:** 5,767,610
- **2025 year-over-year growth:** 3.01%

These checks helped me confirm that the latest-year growth figure displayed on the dashboard was correct.

The full list of measures is available in [DAX_Measures.md](DAX_Measures.md).

## Dashboard Pages

The Power BI report contains five pages connected through navigation buttons.

### 1. Executive Summary

Provides an overview of the main KPIs, yearly case and death trends, regional distribution, risk levels, monthly patterns, and latest-year growth.

*See the Executive Summary screenshot above.*

### 2. Geographic and Risk Analysis

Focuses on state-level comparisons and geographical patterns. It includes a map, population-versus-incidence analysis, state comparisons for case volume and death rate, and a time-intelligence matrix.

![Geographic Analysis](screenshots/02-geographic-analysis.png)

### 3. Key Insights and Recommendations

Summarises the main patterns observed in the simulation and presents the key takeaways from the analysis.

![Key Insights](screenshots/03-key-insights.png)

### 4. Home

Provides the starting point for navigating through the report.

![Home](screenshots/04-home.png)

### 5. Thank You

The closing page of the report.

## Key Findings from the Simulation

The following observations come from the synthetic dataset and should not be interpreted as official Nigerian malaria statistics.

- The North West accounted for approximately **26.93%** of simulated cases.
- The dataset showed higher case values during the May–October period used for the rainy-season assumption.
- Very High Risk states accounted for a large share of simulated case volume under the thresholds used.
- Kano and Lagos had the highest simulated case volumes.
- Bauchi and Borno showed higher simulated death rates.
- Case volume and death rate were analysed separately because a high number of cases does not automatically mean a state has the highest death rate.

## Limitations

There are a few things to keep in mind when interpreting this dashboard:

- The dataset was created for this project and is not real surveillance data.
- Population, case incidence, burden, seasonality, and mortality values rely on simulation assumptions.
- Risk categories were defined for this project.
- The map uses Power BI's built-in geographic mapping rather than a custom shapefile.
- The dataset does not include age groups, weather conditions, healthcare facilities, treatment information, or geographic coordinates.
- The simulation contains relatively few records in the Low Risk category.

The dashboard demonstrates my approach to data analysis and reporting. Its figures should not be used for public health decisions.

## Project Workflow

My workflow for this project was:

1. Create the synthetic dataset.
2. Prepare and transform the data in Power Query.
3. Build the data model and table relationships.
4. Create DAX measures.
5. Check the calculations against known totals and previous periods.
6. Build the report pages and visuals.
7. Review the results and document the limitations.

## Repository Contents

```text
National-Malaria-Surveillance-Dashboard/
│
├── README.md
├── National_Malaria_Surveillance_Dashboard.pbix
├── Malaria_Dashboard_Project_Summary.docx
├── DAX_Measures.md
│
└── screenshots/
    ├── 01-executive-summary.png
    ├── 02-geographic-analysis.png
    ├── 03-key-insights.png
    ├── 04-home.png
    └── 05-thank-you.png
```

## How to View the Dashboard

1. Download `National_Malaria_Surveillance_Dashboard.pbix`.
2. Open the file in Power BI Desktop.
3. Use the navigation buttons to move between the report pages.
4. Review the screenshots in this README for a quick look at the dashboard.
5. Open `DAX_Measures.md` to review the main calculations.

## Tools Used

- **Power BI:** Report and dashboard development
- **Power Query:** Data preparation and transformation
- **DAX:** Measures, calculations, and time intelligence
- **Microsoft Excel:** Supporting data work

## What I Learned

This project taught me that building a dashboard involves much more than arranging charts on a page.

I had to think about what each record represented, how the tables should relate to one another, how to calculate changes over time, and how to check whether the numbers were correct.

I also learned the importance of explaining where data comes from and being clear about what the results can and cannot tell us.

## Author

**Omotosho Johnson**

Computer Engineering | Data Analytics | Business Intelligence

Working with Excel, SQL, Power BI, Power Query, and DAX.

[Connect with me on LinkedIn](https://www.linkedin.com/in/jaythebianalyst/)

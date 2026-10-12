# DAX Measures: Malaria Analysis

DAX measures used in the Malaria Power BI report, grouped by the measure tables in the model.

## Data Model

| Table | Type | Purpose |
|---|---|---|
| `Fact_Malaria` | Fact | Malaria cases, deaths, population and related metrics by state and month |
| `Dim_Calendar` | Dimension | Date table used for all time intelligence |
| `Dim_State` | Dimension | State-level attributes, including `Base Population (2015)` |

### Key columns in `Fact_Malaria`

| Column | Description |
|---|---|
| `Malaria Cases` | Number of reported malaria cases |
| `Malaria Deaths` | Number of malaria deaths |
| `Population` | Population for the record |
| `Fact_Date` | Record date |
| `Year`, `Quarter`, `Month`, `Month No` | Time attributes |
| `State`, `Region` | Geography |
| `Rainy Season`, `Risk Level` | Categorical attributes |
| `Incidence per 1,000`, `Mortality Rate`, `Annual Growth Rate (%)` | Pre-computed rates |
| `Burden Factor`, `Seasonal Factor`, `Variation Factor`, `Malaria Burden` | Modelling and burden fields |

## Measure Tables

| Measure Table | Measures |
|---|---|
| Fact_Malaria (core) | Total Malaria Cases, Total Deaths, Average Incidence, Death Rate, Total Population, Incidence Rate, Peak Year Volume |
| Month-over-Month Analysis | Previous Month Cases, MoM Cases Change, MoM Cases Growth % |
| Rolling 12 Months | Rolling 12 Month Cases |
| Time Intelligence | Previous Year Cases, YoY Cases Growth %, Latest Year YoY Growth %, YoY Cases Change, YoY Test % |
| YTD Analysis | YTD Cases, Previous Year YTD Cases, YTD YoY Growth % |

---

## Core Measures

```dax
1. Total Malaria Cases = SUM ( 'Fact_Malaria'[Malaria Cases] )
```

```dax
2. Total Deaths = SUM ( 'Fact_Malaria'[Malaria Deaths] )
```

```dax
3. Average Incidence =
DIVIDE (
    [1. Total Malaria Cases],
    SUM ( Dim_State[Base Population (2015)] ),
    0
) * 1000
```

```dax
4. Death Rate = DIVIDE ( [2. Total Deaths], [1. Total Malaria Cases], 0 )
```

```dax
5. Total Population = SUM ( 'Fact_Malaria'[Population] )
```

```dax
6. Incidence Rate =
DIVIDE ( [1. Total Malaria Cases], [5. Total Population], 0 ) * 1000
```

```dax
7. Peak Year Volume =
MAXX (
    VALUES ( 'Dim_Calendar'[Year] ),
    CALCULATE ( [1. Total Malaria Cases] )
)
```

---

## Month-over-Month Analysis

```dax
1. Previous Month Cases =
CALCULATE (
    [1. Total Malaria Cases],
    DATEADD (
        'Dim_Calendar'[Date],
        -1,
        MONTH
    )
)
```

```dax
2. MoM Cases Change =
IF (
    ISBLANK ( [1. Previous Month Cases] ),
    BLANK (),
    [1. Total Malaria Cases] - [1. Previous Month Cases]
)
```

```dax
3. MoM Cases Growth % =
IF (
    ISBLANK ( [1. Previous Month Cases] ),
    BLANK (),
    DIVIDE (
        [2. MoM Cases Change],
        [1. Previous Month Cases]
    )
)
```

Measure table placeholder:

```dax
Month-over-Month Analysis = ROW ( "Column", BLANK () )
```

---

## Rolling 12 Months

```dax
1. Rolling 12 Month Cases =
CALCULATE (
    [1. Total Malaria Cases],
    DATESINPERIOD (
        'Dim_Calendar'[Date],
        MAX ( 'Dim_Calendar'[Date] ),
        -12,
        MONTH
    )
)
```

---

## Time Intelligence (Year-over-Year)

```dax
1. Previous Year Cases =
CALCULATE (
    [1. Total Malaria Cases],
    SAMEPERIODLASTYEAR ( 'Dim_Calendar'[Date] )
)
```

```dax
YoY Cases Change =
IF (
    ISBLANK ( [1. Previous Year Cases] ),
    BLANK (),
    [1. Total Malaria Cases] - [1. Previous Year Cases]
)
```

```dax
3. YoY Cases Growth % =
IF (
    ISBLANK ( [1. Previous Year Cases] ),
    BLANK (),
    DIVIDE (
        [YoY Cases Change],
        [1. Previous Year Cases]
    )
)
```

```dax
Latest Year YoY Growth % =
VAR LatestYear =
    MAX ( 'Dim_Calendar'[Year] )

VAR CurrentYearCases =
    CALCULATE (
        [1. Total Malaria Cases],
        'Dim_Calendar'[Year] = LatestYear
    )

VAR PreviousYearCases =
    CALCULATE (
        [1. Total Malaria Cases],
        'Dim_Calendar'[Year] = LatestYear - 1
    )

RETURN
    DIVIDE (
        CurrentYearCases - PreviousYearCases,
        PreviousYearCases
    )
```

```dax
YoY Test % = [3. YoY Cases Growth %] * 100
```

---

## YTD Analysis

```dax
1. YTD Cases =
TOTALYTD (
    [1. Total Malaria Cases],
    'Dim_Calendar'[Date]
)
```

```dax
2. Previous Year YTD Cases =
CALCULATE (
    [1. YTD Cases],
    SAMEPERIODLASTYEAR ( 'Dim_Calendar'[Date] )
)
```

```dax
3. YTD YoY Growth % =
IF (
    ISBLANK ( [2. Previous Year YTD Cases] ),
    BLANK (),
    DIVIDE (
        [1. YTD Cases] - [2. Previous Year YTD Cases],
        [2. Previous Year YTD Cases]
    )
)
```

---

## Notes

- Measures are numbered (`1.`, `2.`, `3.`) so they sort in dependency order in the field list.
- Growth and change measures return `BLANK()` when there is no prior period, so charts don't show misleading zeros.
- `DIVIDE` is used instead of `/` to handle divide-by-zero safely.
- Time intelligence functions require `Dim_Calendar` to be marked as a date table and related to `Fact_Malaria`.
- `Average Incidence` and `Incidence Rate` are expressed per 1,000 population.
- `YoY Test %` is the same value as `3. YoY Cases Growth %` scaled by 100, used to check the percentage output.
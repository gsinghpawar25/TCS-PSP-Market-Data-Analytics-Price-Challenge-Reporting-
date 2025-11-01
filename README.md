# Market Data Analytics: Price Challenge Reporting

A comprehensive data engineering project for analyzing multi-vendor financial security pricing data, detecting price anomalies, and generating quality metrics for Power BI dashboarding.

## 📋 Project Overview

This project processes and analyzes market price feeds from **10 global vendors** covering **800+ securities** across **10 major exchanges** (London, Sydney, Frankfurt, NYC, Oslo, Zurich, Stockholm, Amsterdam, Toronto, Tokyo). The analysis spans from **October 2023 to October 2025**, delivering **20,000 price records** with full quality validation, outlier detection, and vendor performance benchmarking.

### Key Objectives

- **Price Normalization**: Convert multi-currency pricing to USD for standardized comparison
- **Data Quality Assessment**: Identify missing values, duplicates, and data inconsistencies
- **Vendor Performance Benchmarking**: Measure precision rates and price variation patterns across vendors
- **Outlier Detection**: Categorize price deviations into severity levels (Exact Match, Low, Medium, High)
- **Time Intelligence**: Enable temporal analysis with rich date dimensions
- **Power BI Integration**: Generate optimized fact and dimension tables for interactive dashboarding

---

## 🗂️ Project Architecture

### Input Dataset Structure

| Column | Type | Description |
|--------|------|-------------|
| SecurityId | String | Unique security identifier |
| VendorID | Integer | Vendor numeric code |
| VendorCode | String | Vendor name (Bloomberg, FactSet, MSCI, etc.) |
| SourceFeedID | Integer | Feed identifier within vendor |
| PriceType | String | Price category (Ask, Bid, Close, Open) |
| ExchangeCode | String | Exchange code (LON, FRA, NYC, SYD, OSL, ZUR, STO, AMS, TOR, TOK) |
| PriceDate | Date | Date of price record |
| CurrencyCode | String | Currency of original price (AUD, CHF, EUR, GBP, JPY, NOK, DKK, CAD, SEK, USD) |
| CurrencyConversionRate | Float | Exchange rate to USD |
| Price | Float | Original price in source currency |

### Dataset Scope

- **20,000** total records
- **800** unique securities
- **10** vendors (Bloomberg, Markit, S&P, Reuters, Refinitiv, FactSet, MSCI, Morningstar, ICE_Data, NASDAQ)
- **30** unique source feeds
- **4** price types (Ask, Bid, Close, Open)
- **10** global exchanges
- **10** currency codes
- **731** unique trading dates

---

## 🔄 Data Processing Pipeline

### Phase 1: Data Loading & Cleaning

```
Step 1.1: Initial Dataset Inspection
├── Load raw CSV (20,000 rows × 10 columns)
├── Check data types and structure
├── Verify shape and memory usage
└── Display basic statistical summaries

Step 1.2: Data Quality Assessment
├── Identify missing values (Result: 0 missing)
├── Detect duplicate records (Result: 0 duplicates)
├── Check for key combination duplicates
└── Validate data integrity

Step 1.3: Data Transformation
├── Convert PriceDate to datetime format
├── Create PriceUSD (normalized to USD)
│   └── Formula: Price × CurrencyConversionRate
├── Sort data by Security, Date, Type, Exchange, Vendor
└── Output: 20,000 rows × 11 columns
```

**Key Findings**:
- No missing values or duplicates
- Date range: 2023-10-29 to 2025-10-28
- Price range: $10.07 to $50,140.00
- Consistent vendor coverage across exchanges

---

### Phase 2: Exploratory Data Analysis (EDA)

```
Step 2.1: Vendor Coverage Analysis
├── 800 unique securities
├── 10 vendors with ~2,000 records each
├── Coverage of 10 exchanges per vendor
├── Balanced price type distribution (Ask, Bid, Close, Open)
└── Result: Well-balanced data across all dimensions

Step 2.2: Multi-Vendor Combination Detection
├── Group by SecurityId, PriceType, ExchangeCode, PriceDate
├── Identify records with multiple vendors per group
└── Result: 9 multi-vendor combinations for price comparison
```

---

### Phase 3: Aggregation & Statistical Analysis

```
Step 3.1: Price Benchmarking
├── Group records by: SecurityId, PriceType, ExchangeCode, PriceDate
└── Calculate statistics per group:
    ├── Median Price (primary benchmark)
    ├── Minimum Price
    ├── Maximum Price
    ├── Mean Price
    ├── Standard Deviation
    └── Vendor Count

Step 3.2: Price Variation Calculation
├── Formula: ((Vendor Price - Median Price) / Median Price) × 100
├── Absolute Variation: |Price Variation %|
├── Store for outlier categorization
└── Result: Price deviation % for each record
```

---

### Phase 4: Outlier Detection & Categorization

```
Step 4.1: Outlier Classification
├── Exact Match (0%): Vendor price = Median price
├── Low (<3%): Minor deviation, acceptable variance
├── Medium (3-5%): Moderate deviation, requires attention
└── High (>5%): Significant deviation, flag for investigation

Step 4.2: Concurrent Price Identification
├── Identify when all vendors report identical prices
├── Calculate Precision Rate per vendor
│   └── Formula: (Exact Matches / Total Comparisons) × 100
└── Result: 99.91% overall precision rate
```

---

### Phase 5: Vendor Performance Aggregation

```
Step 5.1: Vendor KPI Calculation
├── Total Comparisons: Count of records per vendor
├── Average Deviation %: Mean of |Price Variation %|
├── Exact Matches: Count of Exact Match 0% records
├── Low Outliers: Count of Low (<3%) records
├── Medium Outliers: Count of Medium (3-5%) records
├── High Outliers: Count of High (>5%) records
└── Precision %: (Exact Matches / Total Comparisons) × 100

Step 5.2: Vendor Ranking
├── Bloomberg: Highest performance (110.00 score)
├── Markit: Strong performance (109.98 score)
└── NASDAQ: Lowest score (109.72 score)
```

---

### Phase 6: Dimension Table Creation

#### DimVendor (Vendor Reference)
```
VendorID | VendorCode
---------|-------------
10       | Bloomberg
20       | Markit
30       | S&P
40       | Reuters
50       | Refinitiv
60       | FactSet
70       | MSCI
80       | Morningstar
90       | ICE_Data
100      | NASDAQ
```

#### DimDate (Time Intelligence)
```
Date       | Year | Quarter | Month | MonthName | Week | Day | DayName | IsWeekend
-----------|------|---------|-------|-----------|------|-----|---------|----------
2023-10-29 | 2023 | 4       | 10    | October   | 43   | 29  | Sunday  | 1
2023-10-30 | 2023 | 4       | 10    | October   | 44   | 30  | Monday  | 0
...
2025-10-28 | 2025 | 4       | 10    | October   | 44   | 28  | Tuesday | 0
```

---

## 📊 Output Tables (Power BI Ready)

### 1. FactPriceDetails (Transaction-Level)
**Purpose**: Detailed drill-down capability in Power BI

**Columns**:
- SecurityId, VendorID, VendorCode, SourceFeedID
- PriceType, ExchangeCode, PriceDate
- PriceUSD, MedianPrice, MinPrice, MaxPrice, MeanPrice, StdDev
- PriceVariationPct, AbsPriceVariationPct
- OutlierCategory, IsConcurrentPrice, VendorCount

**Row Count**: 20,000
**Use Case**: Analyze individual vendor prices against benchmarks, identify anomalies

---

### 2. FactVendorPerformance (Aggregated Metrics)
**Purpose**: Vendor-level KPI dashboard

**Columns**:
- VendorCode, PriceDate, ExchangeCode, PriceType
- TotalComparisons, AvgDeviationPct, AvgAbsDeviation
- ExactMatches, LowOutliers, MediumOutliers, HighOutliers
- PrecisionPct

**Aggregation**: Grouped by Vendor, Date, Exchange, PriceType
**Use Case**: Monitor vendor performance trends, identify systematic issues

---

### 3. DimVendor (Vendor Reference)
**Purpose**: Vendor master data

**Columns**:
- VendorID, VendorCode

---

### 4. DimDate (Date Dimension)
**Purpose**: Time intelligence for temporal analysis

**Columns**:
- Date, Year, Quarter, Month, MonthName, Week, Day, DayName, IsWeekend

**Date Range**: 2023-10-29 to 2025-10-28 (731 unique dates)

---

## 📈 Key Performance Metrics

### Overall Dashboard Metrics

| Metric | Value | Interpretation |
|--------|-------|-----------------|
| **Precision Rate %** | 99.91% | Extremely high data quality |
| **Avg Price Variation %** | 4.4% | Minimal price discrepancy across vendors |
| **High Outliers** | 16 total | Only 0.08% of records are flagged |
| **Total Securities** | 800 | Comprehensive security coverage |
| **Total Records** | 20,000 | Sufficient data volume for analysis |

### Vendor Performance Rankings

| Rank | Vendor | Precision Rate | Avg Deviation | High Outliers |
|------|--------|----------------|---------------|---------------|
| 1 | Bloomberg | 100% | 3% | 0 |
| 2 | Markit | 99.85% | 9% | 0 |
| 3 | Reuters | 99.81% | 6% | 0 |
| 4 | Refinitiv | 99.90% | 1% | 0 |
| 5 | FactSet | 99.90% | 5% | 0 |
| 6 | MSCI | 99.90% | 5% | 1 |
| 7 | NASDAQ | 99.80% | 10% | 4 |
| 8 | Morningstar | 99.80% | 19% | 3 |
| 9 | S&P | 99.85% | 15% | 3 |
| 10 | ICE_Data | 99.90% | 10% | 2 |

### Outlier Distribution

- **Exact Match (0%)**: 19,982 records (99.91%)
- **Low (<3%)**: 0 records (0%)
- **Medium (3-5%)**: 2 records (1%)
- **High (>5%)**: 16 records (8%)

---

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Data Processing** | Python 3.x | Core ETL pipeline |
| **Data Manipulation** | Pandas | Data aggregation & transformation |
| **Numerical Computing** | NumPy | Statistical calculations |
| **Environment** | Jupyter Notebook | Development & documentation |
| **Visualization** | Power BI | Interactive dashboarding |
| **Output Format** | CSV, XLSX | Reporting & BI import |

---

## 🚀 Usage Instructions

### Prerequisites
```bash
pip install pandas numpy jupyter
```

### Running the Analysis

1. **Load and prepare the notebook**:
   ```bash
   jupyter notebook import_dataset.ipynb
   ```

2. **Execute cells sequentially**:
   - Phase 1: Data Loading & Cleaning
   - Phase 2: EDA
   - Phase 3: Aggregation & Statistics
   - Phase 4: Outlier Detection
   - Phase 5: Vendor Performance
   - Phase 6: Generate Output Tables

3. **Generated output files**:
   - `FactPriceDetails.csv` - Transaction-level data (20,000 rows)
   - `FactVendorPerformance.csv` - Aggregated vendor metrics
   - `DimVendor.csv` - Vendor reference
   - `DimDate.csv` - Date dimension (731 dates)

### Power BI Integration

1. Import the four CSV files into Power BI
2. Create relationships:
   - `FactPriceDetails.VendorCode` → `DimVendor.VendorCode`
   - `FactPriceDetails.PriceDate` → `DimDate.Date`
   - `FactVendorPerformance.VendorCode` → `DimVendor.VendorCode`
   - `FactVendorPerformance.PriceDate` → `DimDate.Date`
3. Create dashboards for:
   - Vendor Performance Overview
   - Price Variation Analysis
   - Outlier Tracking
   - Time Series Trends

---

## 📊 Dashboard Views (Power BI)

![Power BI Logo](https://github.com/gsinghpawar25/TCS-PSP-Market-Data-Analytics-Price-Challenge-Reporting-/blob/main/page%201.png)

![Power BI Logo](https://github.com/gsinghpawar25/TCS-PSP-Market-Data-Analytics-Price-Challenge-Reporting-/blob/main/page%202.png)

![Power BI Logo](https://github.com/gsinghpawar25/TCS-PSP-Market-Data-Analytics-Price-Challenge-Reporting-/blob/main/page%203.png)

![Power BI Logo](https://github.com/gsinghpawar25/TCS-PSP-Market-Data-Analytics-Price-Challenge-Reporting-/blob/main/page%204.png)

![Power BI Logo](https://github.com/gsinghpawar25/TCS-PSP-Market-Data-Analytics-Price-Challenge-Reporting-/blob/main/page%205.png)


## 📋 Data Quality Checks

### Validation Results
✅ **No missing values** across all 20,000 records  
✅ **No duplicate records** detected  
✅ **Valid currency codes** for all price conversions  
✅ **Consistent vendor presence** across exchanges  
✅ **Price ranges within expected bounds**  
✅ **All dates fall within defined range** (2023-10-29 to 2025-10-28)  

---

## 🎯 Key Insights

1. **Exceptional Data Quality**: 99.91% precision rate indicates highly reliable vendor feeds
2. **Minimal Price Variance**: Only 4.4% average price variation suggests market consistency
3. **Rare Outliers**: Just 16 high outliers (>5% deviation) across 20,000 records
4. **Vendor Reliability**: All vendors maintain >99.8% precision rates
5. **Balanced Coverage**: Even distribution across 10 exchanges and price types
6. **Temporal Consistency**: Stable performance across 2-year period (2023-2025)

---

## 📝 Code Highlights

### Currency Normalization
```python
df['PriceUSD'] = df['Price'] * df['CurrencyConversionRate']
```

### Outlier Categorization
```python
def categorize_outlier(variation_pct):
    if variation_pct == 0:
        return "Exact Match (0%)"
    elif variation_pct < 3:
        return "Low (less than 3%)"
    elif variation_pct <= 5:
        return "Medium (3-5%)"
    else:
        return "High (greater than 5%)"
```

### Precision Calculation
```python
precision_by_vendor = resultdf.groupby('VendorCode').agg({
    'IsConcurrentPrice': ['sum', 'count']
}).reset_index()

precision_by_vendor['PrecisionPercentage'] = \
    (precision_by_vendor['IsConcurrentPrice']['sum'] / 
     precision_by_vendor['IsConcurrentPrice']['count']) * 100
```

---

## 📁 File Structure

```
market-data-analytics/
├── import_dataset.ipynb              # Main Jupyter notebook
├── Fact_Price_Details.csv            # Transaction-level output
├── Fact_Vendor_Performance.csv       # Aggregated vendor metrics
├── Dim_Vendor.csv                    # Vendor reference table
├── Dim_Date.csv                      # Date dimension table
├── README.md                         # Project documentation
└── dashboard_screenshots/            # Power BI dashboard images
    ├── vendor_performance_overview.jpg
    ├── exchange_analysis.jpg
    ├── time_series_trends.jpg
    └── price_challenge_reporting.jpg
```

---

## 🔍 Troubleshooting

### Issue: Memory error when loading large dataset
**Solution**: Use chunking or filter data by date range before processing

### Issue: Currency conversion rates producing negative values
**Solution**: Verify conversion rates are positive; check source data quality

### Issue: Power BI not recognizing date fields
**Solution**: Ensure DimDate imported with Date column as Date/Time type

---

## 📚 References & Documentation

- **Pandas Documentation**: https://pandas.pydata.org/docs/
- **Power BI Best Practices**: Microsoft Power BI Documentation
- **Financial Data Standards**: ISO 4217 Currency Codes

---

## 👥 Author & Contributors

**Project**: Market Data Analytics - Price Challenge Reporting  
**Created**: October 2025  
**Purpose**: Multi-vendor price quality assessment and vendor benchmarking  

---

## 📄 License

This project is provided as-is for educational and analytical purposes.

---

## 🤝 Support & Feedback

For issues, feature requests, or questions regarding this project:
- Review the notebook execution logs
- Verify input data format matches specifications
- Check Power BI relationships are correctly configured

---

**Last Updated**: October 31, 2025  
**Data Period Covered**: October 29, 2023 - October 28, 2025  
**Total Records Processed**: 20,000  
**Overall Data Quality Score**: 99.91% (Precision Rate)

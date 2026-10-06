# Procurement Dashboard Analysis

Power BI project documentation, generated from the model in `Procurement Dashboard.pbix`.

> **Note:** This document describes what is actually in the Power BI model. The model's tables and measures cover **sales, customers, products and returns**. No supplier, purchase-order or vendor-spend tables were found. Sections marked *TBC* need your input.

## 1. Project overview

| Item | Detail |
|---|---|
| Report file | `powerbi/Procurement Dashboard.pbix` |
| Tool | Power BI Desktop |
| Model type | Tabular, Import mode |
| Business objective | *TBC: add from requirement documents* |
| Intended users | *TBC* |
| Data sources | *TBC: list source files in `data/`* |

## 2. Repository structure

```
ProcurementDashboardAnalysis/
├── README.md
├── data/          # source data files
├── docs/          # requirement documents
├── case-study/    # case study write-up
└── powerbi/       # .pbix report
```

## 3. Data model

### Tables

| Table | Type | Description |
|---|---|---|
| `F_Sales` | Fact | Order lines: order date, stock date, order number, customer, quantity, calculated price and cost |
| `F_Returns` | Fact | Returned items: return date, territory, product, return quantity |
| `M_Customers` | Dimension | Customer demographics (gender, marital status, income, education, occupation, home owner) |
| `M_Products` | Dimension | Product SKU, name, model, color, size, style, cost, price |
| `M_Subcategory` | Dimension | Product subcategory |
| `M_Category` | Dimension | Product category |
| `M_Territories` | Dimension | Region, country, continent |
| `M_Date` | Dimension | Calendar: year, quarter, month, day name, sort-order columns |
| `00_DAX` | Measure table | Holds all report measures |
| `Metric`, `Category`, `Parameter` | Field parameters | Let report users switch the field shown in visuals |

### Relationships

| From (many) | To (one) | Filter direction |
|---|---|---|
| `F_Sales[OrderDate]` | `M_Date[Date]` | Single |
| `F_Sales[CustomerKey]` | `M_Customers[CustomerKey]` | Single |
| `F_Sales[ProductKey]` | `M_Products[ProductKey]` | Single |
| `F_Sales[TerritoryKey]` | `M_Territories[SalesTerritoryKey]` | Single |
| `F_Returns[ReturnDate]` | `M_Date[Date]` | Single |
| `F_Returns[ProductKey]` | `M_Products[ProductKey]` | Single |
| `F_Returns[TerritoryKey]` | `M_Territories[SalesTerritoryKey]` | Single |
| `M_Products[ProductSubcategoryKey]` | `M_Subcategory[ProductSubcategoryKey]` | **Both** |
| `M_Subcategory[ProductCategoryKey]` | `M_Category[ProductCategoryKey]` | **Both** |

The model uses a star schema, with two fact tables sharing the date, product and territory dimensions. Date relationships to `F_Sales[StockDate]` and `M_Customers[BirthDate]` use auto-generated hidden date tables.

### Calculated columns

| Table | Column | Purpose |
|---|---|---|
| `F_Sales` | `Product Price`, `Product Cost` | Line-level price and cost looked up from `M_Products` |
| `M_Customers` | `Age`, `Age Band`, `Customer Segment`, `Customer Tenure` | Customer segmentation |
| `M_Products` | `SalesRowCount` | Number of sales rows per product |

## 4. Measures (table `00_DAX`)

### Volume
| Measure | Logic |
|---|---|
| Total Order Qty | `SUM(F_Sales[Orderquantity])` |
| Total Orders | `COUNTROWS(F_Sales)` |
| Total Unique Orders | `DISTINCTCOUNT(F_Sales[OrderNumber])` |
| Return Qty | `SUM(F_Returns[ReturnQuantity])` |

### Financial
| Measure | Logic |
|---|---|
| Total Revenue | `SUMX(F_Sales, Orderquantity * RELATED(M_Products[ProductPrice]))` |
| Total Cost | `SUMX(F_Sales, Orderquantity * RELATED(M_Products[ProductCost]))` |
| Total Profit | `[Total Revenue] - [Total Cost]` |
| Avg Order Value | `DIVIDE([Total Revenue], [Total Order Qty])` |

### Time intelligence
| Measure | Logic |
|---|---|
| TotalYTD Qty / DatesYTD Qty | Year-to-date quantity |
| Total Order Qty QTD / DatesQTD Qty | Quarter-to-date quantity |
| Total Order Qty MTD | Month-to-date quantity |
| Same Period Last Year | Quantity for the same period last year |
| Total Order Qty M-1, Return Qty M-1 | Previous-month values |
| DatesYTD Qty LY, DatesQTD Qty PQ | Prior-year YTD and prior-quarter QTD |
| Revenue / Cost / Profit / Return / Customer Last Year | Prior-year comparisons |

### Year-over-year
Order YoY %, Revenue YoY%, Cost YoY%, Profit YoY %, Return YoY%, Customer YoY%, Avg Rev YoY%. Each is `DIVIDE(current - last year, last year)`. `YoY Color` returns green (`#1B8A3C`) for growth and red (`#D93B3B`) for decline, for conditional formatting.

### Customer
Total customer, Avg Rev per Customer, Avg Rev, Average Customer Tenure, Customer Tenure, Customer Lifetime Value.

## 5. Observations and suggested fixes

1. **Hard-coded year:** `Current Year Order Qty` is fixed to `M_Date[Year] = 2023`. Replace with a dynamic year.
2. **Duplicate measures:** `Total Revenue` and `Total Revenue 2` (and `Total Cost` / `Total Cost 2`) are calculated differently. Keep one definition.
3. **Stray measure:** `VAR AvgRev` looks like a leftover from testing and can be deleted.
4. **Bidirectional filters** on the product, subcategory and category relationships can cause ambiguity and slower queries. Use single direction unless it is needed.
5. **Customer Lifetime Value** is calculated as Avg Order Value × Total Order Qty, which is not a standard lifetime value. Confirm the intended definition.
6. **Customer Tenure** is measured from first order date to `TODAY()`, so values change daily.
7. **Personal data:** `M_Customers` contains `EmailAddress` and `BirthDate`. Remove or anonymise these before publishing to a public GitHub repo.
8. **Scope mismatch:** the model contains no procurement entities such as suppliers, purchase orders, spend or delivery performance. Compare against the requirement documents.

## 6. Business requirements

*TBC: paste or summarise requirements here, and map each one to a measure or visual.*

| ID | Requirement | Measure / Visual | Status |
|---|---|---|---|
| R1 | | | |

## 7. How to open the report

1. Install Power BI Desktop (Windows).
2. Open `powerbi/Procurement Dashboard.pbix`.
3. Update data source paths if prompted (Home > Transform data > Data source settings).

## 8. Author

Maitreyee

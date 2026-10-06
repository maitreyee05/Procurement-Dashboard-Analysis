# DAX Measures: Procurement Dashboard Analysis

All measures live in the `00_DAX` table of `Procurement Dashboard.pbix`. They are grouped below by purpose.

**Tables used:** `F_Sales`, `F_Returns`, `M_Products`, `M_Customers`, `M_Date`

---

## 1. Volume measures

### Total Order Qty
Total units ordered.
```dax
Total Order Qty = SUM(F_Sales[Orderquantity])
```

### Total Orders
Number of order lines.
```dax
Total Orders = COUNTROWS(F_Sales)
```

### Total Unique Orders
Number of distinct orders.
```dax
Total Unique Orders = DISTINCTCOUNT(F_Sales[OrderNumber])
```

### Return Qty
Total units returned.
```dax
Return Qty = SUM(F_Returns[ReturnQuantity])
```

---

## 2. Financial measures

### Total Revenue
Quantity multiplied by the product's selling price.
```dax
Total Revenue =
SUMX(
    F_Sales,
    F_Sales[Orderquantity] * RELATED(M_Products[ProductPrice])
)
```

### Total Cost
Quantity multiplied by the product's unit cost.
```dax
Total Cost =
SUMX(
    F_Sales,
    F_Sales[Orderquantity] * RELATED(M_Products[ProductCost])
)
```

### Total Profit
```dax
Total Profit = [Total Revenue] - [Total Cost]
```

### Avg Order Value
Revenue per unit ordered.
```dax
Avg Order Value = DIVIDE([Total Revenue], [Total Order Qty])
```

### Total Revenue 2 / Total Cost 2
Alternative versions based on calculated columns in `F_Sales`. They duplicate the measures above and may be removed.
```dax
Total Revenue 2 = SUM(F_Sales[Product Price])
Total Cost 2    = SUM(F_Sales[Product Cost])
```

---

## 3. Time intelligence

### Period-to-date
```dax
TotalYTD Qty = TOTALYTD([Total Order Qty], M_Date[Date])

Total Order Qty QTD = TOTALQTD([Total Order Qty], M_Date[Date])

Total Order Qty MTD = TOTALMTD([Total Order Qty], M_Date[Date])

DatesYTD Qty = CALCULATE([Total Order Qty], DATESYTD(M_Date[Date]))

DatesQTD Qty = CALCULATE([Total Order Qty], DATESQTD(M_Date[Date]))
```

### Prior period
```dax
Same Period Last Year =
CALCULATE([Total Order Qty], SAMEPERIODLASTYEAR(M_Date[Date]))

Total Order Qty M-1 =
VAR x = CALCULATE([Total Order Qty], DATEADD(M_Date[Date], -1, MONTH))
RETURN
    IF(HASONEVALUE(M_Date[MonthYear]), x, BLANK())

DatesYTD Qty LY =
CALCULATE([DatesYTD Qty], SAMEPERIODLASTYEAR(M_Date[Date]))

DatesQTD Qty PQ =
CALCULATE([DatesQTD Qty], PARALLELPERIOD(M_Date[Date], -1, QUARTER))

DatesQTD Qty PQ-DTSADD =
CALCULATE([Total Order Qty], DATEADD(M_Date[Date], -1, QUARTER))

Return Qty M-1 =
CALCULATE([Return Qty], DATEADD(M_Date[Date], -1, MONTH))

Return last year =
CALCULATE([Return Qty], SAMEPERIODLASTYEAR(M_Date[Date]))
```

### Fixed year
> Warning: the year is hard-coded. Replace it with a dynamic value (for example `MAX(M_Date[Year])`).
```dax
Current Year Order Qty =
CALCULATE(
    SUM(F_Sales[Orderquantity]),
    M_Date[Year] = 2023
)
```

### Last-year financials
```dax
Revenue Last Year = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(M_Date[Date]))
Cost Last Year    = CALCULATE([Total Cost],    SAMEPERIODLASTYEAR(M_Date[Date]))
Profit Last Year  = CALCULATE([Total Profit],  SAMEPERIODLASTYEAR(M_Date[Date]))
```

---

## 4. Year-over-year growth

Each measure returns `(current - last year) / last year`, and `DIVIDE` handles a zero or blank denominator.

```dax
Order YoY % =
DIVIDE([Total Order Qty] - [Same Period Last Year], [Same Period Last Year])

Return YoY% =
DIVIDE([Return Qty] - [Return last year], [Return last year])

Revenue YoY% =
DIVIDE([Total Revenue] - [Revenue Last Year], [Revenue Last Year])

Cost YoY% =
DIVIDE([Total Cost] - [Cost Last Year], [Cost Last Year])

Profit YoY % =
DIVIDE([Total Profit] - [Profit Last Year], [Profit Last Year])

Customer YoY% =
DIVIDE([Total customer] - [Customer LY], [Customer LY])

Avg Rev YoY% =
DIVIDE([Avg Rev per Customer] - [Avg Rev LY], [Avg Rev LY])
```

### YoY Color
Returns a hex color for conditional formatting: green for growth, red for decline.
```dax
YoY Color =
IF([Order YoY %] >= 0, "#1B8A3C", "#D93B3B")
```

---

## 5. Customer measures

```dax
Total customer = DISTINCTCOUNT(F_Sales[CustomerKey])

Customer LY =
CALCULATE([Total customer], SAMEPERIODLASTYEAR(M_Date[Date]))

Avg Rev per Customer = DIVIDE([Total Revenue], [Total customer])

Avg Rev LY =
CALCULATE([Avg Rev per Customer], SAMEPERIODLASTYEAR(M_Date[Date]))

Avg Rev =
AVERAGEX(FILTER(ALL(M_Customers), [Total Revenue] > 0), [Total Revenue])

Customer Tenure =
DATEDIFF(
    CALCULATE(MIN(F_Sales[OrderDate])),
    TODAY(),
    MONTH
)

Average Customer Tenure =
AVERAGEX(FILTER(ALL(M_Customers), [Total Revenue] > 0), [Customer Tenure])

Customer Lifetime Value = [Avg Order Value] * [Total Order Qty]
```

> `Customer Tenure` uses `TODAY()`, so it changes every day. `Customer Lifetime Value` is not a standard CLV formula, so confirm the intended definition.

---

## 6. Housekeeping

| Item | Recommendation |
|---|---|
| `VAR AvgRev` | Looks like a leftover test measure, so delete it. |
| `Total Revenue 2`, `Total Cost 2` | Keep only one definition of each. |
| `Current Year Order Qty` | Replace the hard-coded 2023. |

---

## 7. Measure index

| Group | Count |
|---|---|
| Volume | 4 |
| Financial | 6 |
| Time intelligence | 16 |
| Year-over-year | 8 |
| Customer | 8 |
| Housekeeping | 1 |
| **Total** | **43** |

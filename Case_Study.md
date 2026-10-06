# Case Study: Sales, Returns, Customers and Territories

**Project:** Procurement Dashboard Analysis (Power BI)
**Author:** Maitreyee
**Data period:** 1 January 2021 to 30 June 2023

---

## 1. Background

A product company sells bikes, accessories and clothing across six countries. Management needs a single dashboard to track how sales are performing, which products and markets drive profit, how many items come back as returns, and which customers matter most. The data was modelled in Power BI as a star schema with two fact tables (`F_Sales`, `F_Returns`) and dimension tables for customers, products, territories and dates.

## 2. Business problem

Leadership could not easily answer four questions:

1. Is revenue growing, and how profitable is it?
2. Which territories and product categories contribute the most?
3. How big is the returns problem, and where does it occur?
4. Which customer groups generate the value, and which are at risk?

## 3. Objectives

- Build an interactive dashboard that answers the questions above.
- Track year-over-year performance for orders, revenue, cost, profit, returns and customers.
- Segment customers to support targeted marketing and retention.

## 4. Data and approach

| Item | Detail |
|---|---|
| Tool | Power BI Desktop, DAX |
| Fact tables | `F_Sales` (order lines), `F_Returns` (returned quantities) |
| Dimensions | Customers, Products, Subcategory, Category, Territories, Date |
| Measures | 43 DAX measures covering volume, financials, time intelligence, YoY growth and customer metrics |

**Method:** loaded the source tables, built relationships (many-to-one from facts to dimensions), created a date table for time intelligence, wrote DAX measures, added customer segments as calculated columns, and designed visuals for each business question.

**Definitions used:**
- *Revenue* = order quantity × product list price
- *Cost* = order quantity × product unit cost
- *Profit* = revenue − cost
- *Return rate* = returned units ÷ ordered units

## 5. Key findings

### 5.1 Overall performance

| Metric | Value |
|---|---|
| Revenue | $24.91 million |
| Cost | $14.46 million |
| Profit | $10.46 million (about 42% margin) |
| Units ordered | 84,174 |
| Unique orders | 25,164 |
| Unique customers | 17,416 |
| Units returned | 1,828 (about 2.2% of units) |

### 5.2 Revenue and returns by year

| Year | Revenue | Profit | Units | Returned units | Return rate |
|---|---|---|---|---|---|
| 2021 | $6.40M | $2.60M | 2,630 | 86 | 3.3% |
| 2022 | $9.32M | $3.97M | 36,230 | 770 | 2.1% |
| 2023 (Jan to Jun only) | $9.19M | $3.89M | 45,314 | 972 | 2.1% |

- In six months, 2023 revenue almost matched all of 2022, so the year is on course to be the strongest yet.
- Units grew much faster than revenue because accessories and clothing were added to a business that started with bikes (see 5.3).
- The return rate fell from 3.3% in 2021 to about 2.1% and has stayed there.

### 5.3 Product categories

| Category | Revenue | Share of revenue | Margin | Units | Return rate |
|---|---|---|---|---|---|
| Bikes | $23.64M | 94.9% | 41.1% | 13,929 | 3.1% |
| Accessories | $0.91M | 3.6% | 62.8% | 57,809 | 2.0% |
| Clothing | $0.37M | 1.5% | 44.3% | 12,436 | 2.2% |

- Bikes produce almost all revenue from only 17% of units, so the business depends heavily on one category.
- Accessories sell in volume with the best margin (about 63%), but each sale is small.
- Bikes have the highest return rate (3.1%) and, because of their price, the largest return value.
- The Components category has no sales in the data.

### 5.4 Territories

| Country | Revenue | Share | Margin | Units | Return rate |
|---|---|---|---|---|---|
| United States | $7.94M | 31.9% | 42.4% | 29,823 | 2.1% |
| Australia | $7.42M | 29.8% | 41.5% | 17,951 | 2.3% |
| United Kingdom | $2.90M | 11.7% | 41.8% | 9,694 | 2.1% |
| Germany | $2.52M | 10.1% | 41.8% | 7,950 | 2.1% |
| France | $2.36M | 9.5% | 41.9% | 7,862 | 2.4% |
| Canada | $1.77M | 7.1% | 42.8% | 10,894 | 2.2% |

- The United States and Australia together account for about 62% of revenue.
- Margins are consistent across countries (41.5% to 42.8%), so differences are driven by volume rather than profitability.
- Return rates are similar everywhere, with France the highest at 2.4%. Returns do not look like a single-market problem.
- Canada sells the third-highest number of units but has the lowest revenue, which suggests a mix weighted toward low-priced items.

### 5.5 Customer segments

| Segment | Customers | Share of customers | Revenue | Share of revenue | Avg revenue per customer |
|---|---|---|---|---|---|
| High Value | 4,612 | 26.5% | $18.44M | 74.0% | $3,998 |
| At Risk | 1,626 | 9.3% | $3.63M | 14.6% | $2,235 |
| Low Value | 8,757 | 50.3% | $2.13M | 8.5% | $243 |
| Loyal | 2,421 | 13.9% | $0.71M | 2.9% | $295 |

- A quarter of customers generate nearly three-quarters of revenue.
- The At Risk group has a high average spend ($2,235), so winning these customers back is worth more than the group's size suggests.
- Half of all customers are Low Value, with average spend of about $243.

## 6. Recommendations

1. **Protect and grow the bike business, but diversify.** Dependence on one category (95% of revenue) is a risk. Promote accessories and clothing alongside bike sales.
2. **Use accessories as a profit lever.** At about 63% margin, bundles or add-on offers for bike buyers could lift profit.
3. **Investigate bike returns.** Review return reasons by product and model to find quality, sizing or description problems, since bikes have the highest return rate.
4. **Start a retention programme for At Risk customers.** Targeted offers and follow-up would protect a segment worth $3.6M.
5. **Review the Components category.** Decide whether it should be stocked, tracked or removed from the model.
6. **Grow under-penetrated markets.** France, Germany and Canada have healthy margins but much lower revenue than the United States and Australia.

## 7. Limitations

- Revenue uses list prices, so discounts and promotions are not reflected.
- Returns are recorded as quantities only, so the monetary value of returns is not measured.
- 2023 covers only six months, so full-year comparisons need care.
- The rules behind the customer segments (High Value, At Risk, and so on) are defined in the model and should be documented.
- The data covers sales, returns, customers and territories. It has no supplier or purchasing data.

## 8. Next steps

- Add a return-value measure (returned units × price).
- Add a forecast for the second half of 2023.
- Build a customer drill-through page for At Risk customers.
- Document the segment definitions and refresh process in the README.

## 9. Skills demonstrated

Data modelling (star schema), DAX (time intelligence, iterators, YoY analysis), customer segmentation, KPI design, dashboard design and business storytelling.

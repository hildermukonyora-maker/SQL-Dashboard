#SQLSALES&CUSTOMERANALYTICS#DATAVISUALISATION#BUSINESSINTELLIGENCE#DATAANALYST#SQL#LEARNANDGROW
#AdventureWorksLT2025#PreparedbyHildaMukonyora#SQLServerManagementStudio#TSQL#SQLDATASET"C:\Users\hello\Desktop\data projects\SQL\AdventureWorksLT — SQL Analytics Project work.sql"

Executive Summary 
This project applies T-SQL window functions, CTEs, and aggregate analysis to the AdventureWorksLT2025 sample database to 
answer five common commercial questions: who the most valuable customers are, how well the business retains them, how 
sales are trending month over month, which product categories are actually profitable, and which products drive the bulk of 
revenue. The analysis uses NTILE, LAG, running-total window functions, and conditional aggregation to move beyond simple 
totals into segmentation and trend diagnostics. 
1.  Customer Lifetime Value & Segmentation 
Customers were ranked by lifetime revenue (SUM of TotalDue across orders) and split into four value quartiles using NTILE(4), 
giving a repeatable way to identify a top-tier customer segment for retention or account-management focus. 
Customer                 Orders           Lifetime Value       Avg Order Value     Quartile 
Terry Eminhizer           1                $119,960.82           $119,960.82       1 (Top) 
Krishna Sunkammurali      1                $108,597.95           $108,597.95       1 (Top) 
Christopher Beck          1                $98,138.21            $98,138.21        1 (Top) 
Kevin Liu                 1                $92,663.56            $92,663.56        1 (Top)
Jon Grande                1                $86,222.81            $86,222.81        1 (Top) 
Observation: the top five customers alone contribute over $505,000 in lifetime value, and every customer in the dataset placed 
only a single order — a pattern examined further in Section 2. 

2.  Customer Retention: Repeat vs. One-Time Buyers 
Customers were classified as repeat or one-time buyers based on order count, then aggregated to show the revenue and 
customer-mix contribution of each group. 
Customer Type             Customers           Total Revenue            % of Customers 
One-Time Buyer              32                 $956,303.59               100.0% 
Finding: 100% of the 32 customers in this dataset are one-time buyers — there is currently no repeat-purchase base to 
analyse. This is the single most important flag in the report: with zero repeat customers, retention and loyalty metrics cannot 
be computed until the order history is refreshed with a fuller, multi-order dataset (or extended to the full AdventureWorks 
OLTP tables rather than the LT sample). 

3.  Monthly Sales Trend & Growth Rate 
A CTE aggregated revenue by calendar month, with LAG() used to compare each month to the prior one and compute a 
month-over-month growth percentage. 
Sales Month                 Revenue           Prior Month Revenue       MoM Growth % 
June 2008                 $956,303.59               N/A                     N/A 
Finding: the order data currently spans a single calendar month (June 2008), so no month-over-month trend line exists yet. 
This confirms the pattern in Section 2 — the underlying SalesOrderHeader data in this sample is a narrow slice (32 orders, one 
month) rather than a full transaction history, so trend and retention outputs should be re-run once broader historical data is 
loaded. 

4.  Product Category Profitability 
Revenue and gross profit (LineTotal less standard cost) were calculated by product category to see whether the highest
revenue categories are also the most profitable ones. 
Category                    Revenue             Gross Profit             Margin % 
Touring Bikes               $220,655.38           -$9,147.51              -4.15% 
Road Bikes                  $183,130.30           -$18,616.74             -10.17% 
Mountain Bikes              $170,825.89            $3,179.99               1.86% 
Mountain Frames             $54,949.60             $4,798.93               8.73% 
Shorts                      $3,299.80              $1,205.70               36.54%
Bike Racks                  $2,304.00              $867.84                 37.67% 
Finding: the two highest-revenue categories, Touring Bikes and Road Bikes, are actually loss-making on a standard-cost basis 
(-4.15% and -10.17% margin), while small-ticket accessory categories such as Bike Racks and Shorts carry the strongest 
margins (over 36%). Revenue ranking alone would have masked this — a category-profitability view is essential before 
treating top-line sales as a proxy for performance. 

5.  Top Products & the 80/20 Rule 
A running total and cumulative percentage of revenue (via SUM() OVER with an unbounded preceding window) were 
calculated across all 142 products ordered, ranked by revenue, to test for Pareto concentration. 
Product                           Revenue                   Cumulative % 
Touring-1000 Blue, 60            $37,191.49                    5.25%
Mountain-200 Black, 42           $37,178.84                    10.49% 
Road-350-W Yellow, 48            $36,486.24                    15.64% 
Mountain-200 Black, 38           $35,801.84                    20.69%
Touring-1000 Yellow, 60          $23,413.47                    24.00% 
Finding: concentration is real but not extreme — the top 5 of 142 products (under 4% of the SKU range) already account for a 
quarter of total revenue, and by the 16th product cumulative revenue has passed 50%. This points to a long-tail catalogue 
where a relatively small set of bike and frame models carries a disproportionate share of sales, useful for prioritising stock, 
promotion, and supplier negotiation focus. 

Methodology & Tools 
● Database: AdventureWorksLT2025 sample database, queried via SQL Server Management Studio (T-SQL). 
● Techniques applied: CTEs, JOINs across Customer/SalesOrderHeader/SalesOrderDetail/Product/ProductCategory, 
NTILE() for quartile segmentation, LAG() for period-over-period comparison, and SUM() OVER (ROWS UNBOUNDED 
PRECEDING) for running totals and Pareto analysis. 


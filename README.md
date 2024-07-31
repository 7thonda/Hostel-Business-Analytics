# Hostel-Business-Analytics

## Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Data Vizualizations](#data-vizualizations)
  
  -[Up to date and interactive Viz](#tools)
- [Result and Findings](#results-and-findings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [References](#references)

---

## Project Overview
This repository provides a comprehensive analysis of the business health of the House of Athena hostel. It covers key aspects such as revenue generation, expenses, and occupancy rates. By offering insights into past performance, current status, and future projections, this analysis aims to present a holistic view of the hostel's overall performance.

#### **Overall Performance Dashboard**
![Screenshot_31-7-2024_18410_](https://github.com/user-attachments/assets/58fa135d-13eb-47d3-8b09-bf3811e8a733)

## Data Source
A number of datasets was used to carry out this anaysis and they are as follows:

Revenue Data: This dataset comprises all revenue generated from payments made by clients. It provides a detailed record of the hostel's income. The dataset used for this analysis is the [Revenue_table.csv](https://drive.google.com/file/d/19bkwPs4hOxb06chCvXqvAKxwI4VY3yUi/view?usp=drive_link) file.

**Tenure Data:** The Primary dataset used for this analysis is the [Tenure_table.csv](https://drive.google.com/file/d/17RXxrbw1NDJG7cyL86KkcBhp1BUETO7u/view?usp=drive_link) file. This dataset has the records of the date of payment and the duration of stay of each client.

**Operation Data:** This dataset contains all the expenses made on keeping the business running, e.g the Light bill. the primary dataset used for this analysis is [Operation_table.csv](https://drive.google.com/file/d/1yf6tZ0KVFF2RMZsJrTRnPJ1FECdA1GA2/view?usp=drive_link).

**Maintenance Data:** This dataset contains all the expenses made on mataining the infracstructure if the business, e.g Maintaining the Generator, Air conditioner, etc. The dataset used for this analysis is [Maintenance_table.csv](https://drive.google.com/file/d/1pbXpDNcilW_t4SBSigxrCxAolCvpsYsH/view?usp=drive_link)

**Finance Data:** This dataset has records of all the monies that leaves and enters the business that do not have any direct effect to operation of the business. e.g paying of investors and employees, legal fees, etc.
The dataset used for this analysis is [Finance_table](https://drive.google.com/file/d/1xquLk0lgSt8DSGIk-SH-LKrb2M2UfkBt/view?usp=drive_link).

**Investment Data:** This dataset contains detailed records of all capital expenditures aimed at enhancing the value and operational efficiency of the business. It also records returns on investment. The dataset used for this analysis is [Investement_table.csv](https://drive.google.com/file/d/1YArxUr8GU59ypcyQV8bk6Z9pifNj3Vfs/view?usp=drive_link).

**Customer occupancy and retention rate Data:** These datasets provide detailed information on the occupancy rate and retention rate for each room, tracked on a monthly and yearly basis. The datasets used for this analysis are [Occupancy_rate.csv](https://drive.google.com/file/d/1z-HXOb7ubJEkQss53EAcsgphFHl45m4y/view?usp=drive_link) and [Customer_retention_rate.csv](https://drive.google.com/file/d/1eg3uPCL34AeVhePCOw9GiHbIb8bq8Ur8/view?usp=drive_link)."


## Tools
- Excel : This was used for the data entries and cleaning.
- SQL server : This was used for querying and analysis.
- Power BI: This was used to create vizualizations for the analysis.
  - [click for Interactive Viz](https://app.powerbi.com/links/BiZbVzzbCW?ctid=5fe78ac1-1afe-4009-aa04-a71efb4a5042&pbi_source=linkShare)

### Data Cleaning and Preparation
In the intial data preparation phase, we performed the following tasks:
1. Data entry and Inspection.
2. Handling missing values.
3. Creating new tables
4. Data cleaning and Formating.


### Exploratory Data Analysis
- What is the overall Revenue and profit generation trend?.
    - Monthly, Quarterly and Yearly.
- What is the overall Expenses trend?.
  - Monthly, Quarterly and Yearly.
- What are the peak period for Revenue and profit generation?.
   - Monthly, Quarterly and Yearly.
- What comprises as the peak cost in expenses?.
- What room generates the most and the least amount of revenue?
- What is the occupancy trend for each room?
  - Monthly, Quarterly and Yearly.
- What room type has the most occupancy rate?
- What is the Customer retention rate trend of the business?
    - Quarterly, Yearly.
- The overall top paying clients and the rooms they occupy.


### Data Analysis
#### This calculates the total revenue, gross expense and profit per month
```sql
#This CTE calculates the total reveue generated per month
WITH Revenue_calculation AS (
    SELECT 
        CASE
            WHEN MONTH(Date_credited) >= 8 THEN CONCAT(YEAR(Date_credited), '-', LPAD(MONTH(Date_credited), 2, '0'))
            ELSE CONCAT(YEAR(Date_credited) - 1, '-', LPAD(MONTH(Date_credited), 2, '0'))
        END AS Fiscal_month,
        SUM(rt.Amount_paid) AS Total_revenue
    FROM revenue_table AS rt
    JOIN tenure_table AS tt
    ON rt.Payment_ID = tt.Payment_ID
    GROUP BY 
        CASE
            WHEN MONTH(Date_credited) >= 8 THEN CONCAT(YEAR(Date_credited), '-', LPAD(MONTH(Date_credited), 2, '0'))
            ELSE CONCAT(YEAR(Date_credited) - 1, '-', LPAD(MONTH(Date_credited), 2, '0'))
        END
),

#This CTE calculates the operational cost per month
Operation_Expenses AS (
    SELECT 
        CASE
            WHEN MONTH(Date) >= 8 THEN CONCAT(YEAR(Date), '-', LPAD(MONTH(Date), 2, '0'))
            ELSE CONCAT(YEAR(Date) - 1, '-', LPAD(MONTH(Date), 2, '0'))
        END AS Fiscal_month,
        SUM(Amount) AS OP_expense
    FROM operation_table
    GROUP BY 
        CASE
            WHEN MONTH(Date) >= 8 THEN CONCAT(YEAR(Date), '-', LPAD(MONTH(Date), 2, '0'))
            ELSE CONCAT(YEAR(Date) - 1, '-', LPAD(MONTH(Date), 2, '0'))
        END
),
#This CTE calculates the maintenance cost per month
Maintenance_Expenses AS (
    SELECT 
        CASE
            WHEN MONTH(Date) >= 8 THEN CONCAT(YEAR(Date), '-', LPAD(MONTH(Date), 2, '0'))
            ELSE CONCAT(YEAR(Date) - 1, '-', LPAD(MONTH(Date), 2, '0'))
        END AS Fiscal_month,
        SUM(Amount) AS MT_expenses
    FROM maintenance__table
    GROUP BY 
        CASE
            WHEN MONTH(Date) >= 8 THEN CONCAT(YEAR(Date), '-', LPAD(MONTH(Date), 2, '0'))
            ELSE CONCAT(YEAR(Date) - 1, '-', LPAD(MONTH(Date), 2, '0'))
        END
)
# This expressed the Total revenue, Total gross expenses(operation and maintenance) and the Gross profit by Joining all the CTE with Fiscal_Month
SELECT 
    rc.Fiscal_month,
    rc.Total_revenue,
    COALESCE(oe.OP_expense, 0) + COALESCE(me.MT_expenses, 0) AS gross_expenses,
    rc.Total_revenue - (COALESCE(oe.OP_expense, 0) + COALESCE(me.MT_expenses, 0)) AS gross_profit
FROM 
    Revenue_calculation rc
LEFT JOIN Operation_Expenses oe
    ON rc.Fiscal_month = oe.Fiscal_month
LEFT JOIN Maintenance_Expenses me
    ON rc.Fiscal_month = me.Fiscal_month
ORDER BY rc.Fiscal_month;
```

#### This calculates the total revenue, gross expense and profit per quarter
```sql
#This CTE calculates the total reveue generated per quarter
WITH Revenue_calculation AS (
    SELECT 
        CASE
            WHEN MONTH(Date_credited) IN (8, 9, 10) THEN CONCAT(YEAR(Date_credited), '-Q1')
            WHEN MONTH(Date_credited) IN (11, 12, 1) THEN CONCAT(YEAR(Date_credited) - CASE WHEN MONTH(Date_credited) = 1 THEN 1 ELSE 0 END, '-Q2')
            WHEN MONTH(Date_credited) IN (2, 3, 4) THEN CONCAT(YEAR(Date_credited) - 1, '-Q3')
            ELSE CONCAT(YEAR(Date_credited) - 1, '-Q4')
        END AS Fiscal_quarter,
        SUM(rt.Amount_paid) AS Total_revenue
    FROM revenue_table AS rt
    JOIN tenure_table AS tt
    ON rt.Payment_ID = tt.Payment_ID
    GROUP BY Fiscal_quarter
),
#This CTE calculates the operation cost per quarter
Operation_Expenses AS (
    SELECT 
        CASE
            WHEN MONTH(Date) IN (8, 9, 10) THEN CONCAT(YEAR(Date), '-Q1')
            WHEN MONTH(Date) IN (11, 12, 1) THEN CONCAT(YEAR(Date) - CASE WHEN MONTH(Date) = 1 THEN 1 ELSE 0 END, '-Q2')
            WHEN MONTH(Date) IN (2, 3, 4) THEN CONCAT(YEAR(Date) - 1, '-Q3')
            ELSE CONCAT(YEAR(Date) - 1, '-Q4')
        END AS Fiscal_quarter,
        SUM(Amount) AS OP_expense
    FROM operation_table
    GROUP BY Fiscal_quarter
),
#This CTE calculates the maintenance cost per quarter
Maintenance_Expenses AS (
    SELECT 
        CASE
            WHEN MONTH(Date) IN (8, 9, 10) THEN CONCAT(YEAR(Date), '-Q1')
            WHEN MONTH(Date) IN (11, 12, 1) THEN CONCAT(YEAR(Date) - CASE WHEN MONTH(Date) = 1 THEN 1 ELSE 0 END, '-Q2')
            WHEN MONTH(Date) IN (2, 3, 4) THEN CONCAT(YEAR(Date) - 1, '-Q3')
            ELSE CONCAT(YEAR(Date) - 1, '-Q4')
        END AS Fiscal_quarter,
        SUM(Amount) AS MT_expenses
    FROM maintenance__table
    GROUP BY Fiscal_quarter
)
# This expressed the Total revenue, Total gross expenses(operation and maintenance) and the Gross profit by Joining all the CTE with Fiscal_Quarter
SELECT 
    rc.Fiscal_quarter,
    rc.Total_revenue,
    COALESCE(oe.OP_expense, 0) + COALESCE(me.MT_expenses, 0) AS gross_expenses,
    rc.Total_revenue - (COALESCE(oe.OP_expense, 0) + COALESCE(me.MT_expenses, 0)) AS gross_profit
FROM 
    Revenue_calculation rc
LEFT JOIN Operation_Expenses oe
    ON rc.Fiscal_quarter = oe.Fiscal_quarter
LEFT JOIN Maintenance_Expenses me
    ON rc.Fiscal_quarter = me.Fiscal_quarter
ORDER BY rc.Fiscal_quarter;
```

#### This calculates the total revenue, gross expense and profit per year

```sql
#This CTE calculates the total reveue generated per fiscal year
WITH Revenue_calculation AS (
    SELECT 
        CASE
            WHEN MONTH(Date_credited) >= 8 THEN YEAR(Date_credited)
            ELSE YEAR(Date_credited) - 1
        END AS Fiscal_year,
        SUM(rt.Amount_paid) AS Total_revenue
    FROM revenue_table AS rt
    JOIN tenure_table AS tt
    ON rt.Payment_ID = tt.Payment_ID
    GROUP BY Fiscal_year
),
#This CTE calculates the total operation costs generated per fiscal year
Operation_Expenses AS (
    SELECT 
        CASE
            WHEN MONTH(Date) >= 8 THEN YEAR(Date)
            ELSE YEAR(Date) - 1
        END AS Fiscal_year,
        SUM(Amount) AS OP_expense
    FROM operation_table
    GROUP BY Fiscal_year
),
#This CTE calculates the total Maintenance costs generated per fiscal year
Maintenance_Expenses AS (
    SELECT 
        CASE
            WHEN MONTH(Date) >= 8 THEN YEAR(Date)
            ELSE YEAR(Date) - 1
        END AS Fiscal_year,
        SUM(Amount) AS MT_expenses
    FROM maintenance__table
    GROUP BY Fiscal_year
)
# This expressed the Total revenue, Total gross expenses(operation and maintenance) and the Gross profit by Joining all the CTE with Fiscal year.
SELECT 
    rc.Fiscal_year,
    rc.Total_revenue,
    COALESCE(oe.OP_expense, 0) + COALESCE(me.MT_expenses, 0) AS gross_expenses,
    rc.Total_revenue - (COALESCE(oe.OP_expense, 0) + COALESCE(me.MT_expenses, 0)) AS gross_profit
FROM 
    Revenue_calculation rc
LEFT JOIN Operation_Expenses oe
    ON rc.Fiscal_year = oe.Fiscal_year
LEFT JOIN Maintenance_Expenses me
    ON rc.Fiscal_year = me.Fiscal_year
ORDER BY rc.Fiscal_year;
```
#### This is the top 5 expenses in both mainteance and operation cost.
```sql
# this Code groups the maintenace costs and operation costs and brings out the top 5 from each expense group
(
    SELECT 
        'Maintenance' AS Cost_Type,
        Maintenance AS Category,
        SUM(Amount) AS Total_Cost
    FROM hostel_analysis.maintenance__table
    GROUP BY Maintenance
    ORDER BY Total_Cost DESC
    LIMIT 5
)

UNION ALL

(
    SELECT
        'Operation' AS Cost_Type,
        Description AS Category,
        SUM(Amount) AS Total_Cost
    FROM operation_table
    GROUP BY Description
    ORDER BY Total_Cost DESC
    LIMIT 5
)

ORDER BY Total_Cost DESC;
```

#### total revenue generated per room
```sql
SELECT 
	room_type,
    SUM(Amount_paid) AS total_revenue
 FROM hostel_analysis.revenue_table
 GROUP BY room_type
 ORDER BY total_revenue DESC ;
```
###This codes include the Total occupancy rate, Average occupancy rate per month and average occupancy rate per room.
```sql
#This is the total average occupancy rate of the entire hostel
SELECT 
  AVG(Average_rate)
FROM occupancy_rate;

#This is the average occupancy rate per month of the entire hostel
SELECT
  months,
  AVG(Average_rate) AS average_occupancy_rate
FROM occupancy_rate
GROUP BY months
ORDER BY average_occupancy_rate DESC;

#This is the average occupancy rate per room
SELECT 
	AVG(2_man_room_up) 2_man_room_up ,
	AVG( 2_man_room_down) 2_man_room_down,
	AVG(4_man_room) 4_man_room
FROM hostel_analysis.occupancy_rate;
```
### Data Vizualizations

#### **Reveue Dashboard viz**
![Screenshot_31-7-2024_184152_](https://github.com/user-attachments/assets/da16e8a0-cbac-4929-87a9-d4c6ca80804e)

#### **Expenses Dashboard Viz**
![Screenshot_31-7-2024_184214_](https://github.com/user-attachments/assets/1da9f17e-78ff-4fd3-b934-dc82cd4cad22)

#### **Occupancy and retention rate Dasboard Viz**
![Screenshot_31-7-2024_18439_](https://github.com/user-attachments/assets/9d23c097-eb74-41d3-be72-a1d8eedfc1ca)

#### **Client Information dashboard Viz**
![Screenshot_31-7-2024_191258_](https://github.com/user-attachments/assets/b2f1416f-6887-4d8f-8e88-dace4a1bfd90)



### Result and Findings

The analysis results are summarized as follows:
1. the company's sales has been steadily increasing over the past year, with a noticeable peak during the holiday.
2. Product cateogry A is the best performing ctegory in terms of sales and revenue
3. customer segments with high lifetime valueshould be targeted for marketing purposes.


## Recommendations

Based on the analysis I recommend the following actions:
1. Invest in marketing and promotion during peak sales season to maximize revenue.
2. Focus and expanding and promoting products in category A.
3. implement a customer segmentation strategy to target high LTV customers effectively.


## Limitations
1. The Fiscal year starts in August not January.
I had to remove all zero values from budget and revenue columns because they would have affected the accuracy of my conclusions from the analysis, There are still a few outliers even after the omission even then we can still that there is a positive correlation between both budget and number of votes with revenue.

## References
1. customer retention rate

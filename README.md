## About this project
# Loading and Shaping the Data:
To begin the project I uploaded all CSV files into Power BI and then shaped the data using the power query editor. I went through each data table and ensured all data types were accurate. I created new columns in the power query editor to include full name, birth year, has children (conditional column), discount price (calculated column), avg retail price (group by option), full address (calculated column using merge), area code (calculated column), start of week, name of day, start of month, name of month, quarter of year, year and then replaced all null values within each data set with zeros. I added both the 1997 and 1998 data sets together via a connected folder. I updated all date fields to MM/DD/YYYY format using the modeling tab and made sure all prices or costs were formatted as currency. Lastly, I formatted all store fields as geographical locations.

# Creating the Data Model:
In the relationships view I connected transaction data to the customers, products and stores lookup tables using primary and foreign keys. I connected the transaction data to the calendar lookup table using an active and inactive relationship. I connected the return data to the products, calendar and stores data tables using primary and foreign keys and then connected the stores data to the regions data lookup table as a snowflake schema. Once all of my tables were connected, I ensured all relationships were one-to-many and that the filters were flowing one way and downstream to the primary data table. I also made sure all data tables were connected via shared lookup tables and not directly to each other.
![Model View](https://github.com/npnga02/Maven-Market-Analysis-Power-BI/assets/168510844/986b8c0f-69aa-480b-ad3d-8d0f32c83d8e)

# Adding DAX Measures:
In the data view I added weekend, end of month, current age, priority, short country, house number, price tier and years since remodel using DAX equations.
```
Weekend = IF('Calendar'[Day Name] = "Saturday" || 'Calendar'[Day Name] = "Sunday", "Y", "N")
End of Month = EOMONTH('Calendar'[date], 0)
Current Age = DATEDIFF(Customers[birthdate], TODAY(), YEAR)
Priority = IF(Customers[member_card] = "Golden" && Customers[homeowner] = "Y", "High", "Standard")
Short_Country = UPPER(LEFT(Customers[customer_country], 3))
House_Number = LEFT(Customers[customer_address], SEARCH(" ", Customers[customer_address])-1)
Price_Tier = IF(Products[product_retail_price]>3, "High", IF(Products[product_retail_price]>1, "Mid", "Low"))
Years_Since_Remodel = DATEDIFF(Stores[last_remodel_date], TODAY(), YEAR)
```

Navigating to the reports view, I added quantity sold, quantity returned, total transactions, total returned, return rate, weekend transactions, all transactions, all returns, total revenue, total cost, total profit, unique products, year to date revenue, 60-day revenue, last month transactions, last month revenue, last month profit, last month returns and revenue target.
```
Quanitity Sold = SUM(Transaction_Data[quantity])
Quantity Returned = SUM(Return_Data[quantity])
Total Transactions = COUNTROWS(Transaction_Data)
Total Returns = COUNTA(Return_Data[quantity])
Return Rate = [Quantity Returned] / [Quanitity Sold]
Weekend Transactions = CALCULATE([Total Transactions],'Calendar'[Weekend] = "Y")
All Transactions = CALCULATE([Total Transactions], ALL(Transaction_Data))
All Returns = CALCULATE(Return_Data[Total Returns], ALL(Return_Data))
Total Revenue = SUMX(Transaction_Data, [Quanitity Sold] * RELATED(Products[product_retail_price]))
Total Cost = SUMX(Transaction_Data, [Quanitity Sold] * RELATED(Products[product_cost]))
Total Profit = [Total Revenue] - [Total Cost]
Unique Products = DISTINCTCOUNT(Products[product_name])
YTD Revenue = CALCULATE([Total Revenue], DATESYTD('Calendar'[date]))
60-Day Revenue = CALCULATE([Total Revenue], DATESINPERIOD('Calendar'[date],
MAX('Calendar'[date]), -60, DAY))
Last Months Transaction = CALCULATE([Total Transactions], DATEADD('Calendar'[date], -1, MONTH))
Last Months Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1, MONTH))
Last Months Profit = CALCULATE([Total Profit], DATEADD('Calendar'[date], -1, MONTH))
Last Months Returns = CALCULATE(Return_Data[Total Returns], DATEADD('Calendar'[date], -1, MONTH))
```

# Creating Visuals:
To begin building my visual I started with the main matrix which contains total transactions, total profit, profit margin and return rate by product brand. I applied a Top N filter to only show the top 30 product brands by total transactions. I added three KPI cards to show current month transactions, profit and returns. I then added the map which you can filter down by country. Next to the map I added a tree chart which enables the viewer to drill down to specific cities within each country if they choose to do so. I added a bar chart to show revenue over time and a revenue target meter for the latest month worth of sales.
<img width="899" alt="MavenMarket" src="https://github.com/npnga02/Maven-Market-Analysis-Power-BI/assets/168510844/2a0484eb-fd32-4c10-a55e-04ad035c3c94">

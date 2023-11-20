## <p align="center"> Business Questions
The goal of this analysis is to comprehend and respond to some business issues, obtain insights, and make recommendations to boost income.  

---

1. ### Who are the top 10 customers with more than 30 orders, including their product preferences?  

### Steps : 
•	Return the account name.

•	Retrieve the sum of orders made by each of these customers by different product lines.

•	Sort the results in descending order based on the order sum and limit it to the top 20 customers. 

```sql
SELECT 
    Name,
    SUM(CASE WHEN poster_qty > 30 THEN 1 ELSE 0 END) AS Poster_Count,
    SUM(CASE WHEN gloss_qty > 30 THEN 1 ELSE 0 END) AS Gloss_Count,
    SUM(CASE WHEN standard_qty > 30 THEN 1 ELSE 0 END) AS Standard_Count
FROM orders AS o
JOIN accounts AS a 
ON o.account_id = a.id
GROUP BY 
    Name
ORDER BY 
    Poster_Count DESC, Gloss_Count DESC, Standard_Count DESC
LIMIT 20;
```
### Output 
Name | Poster_Count | Gloss_Count | Standard_Count
-- | -- | -- | --
Leucadia National | 44 | 49 | 65 |  
EOG Resources | 46 | 46 | 62 |  
Mosaic | 34 | 48 | 61 |  
General Dynamics | 42 | 44 | 61 |  
United States Steel | 38 | 41 | 60 |  
Archer Daniels Midland | 39 | 46 | 60 |  
Sysco | 41 | 49 | 59 |  
Supervalu | 39 | 49 | 59 |  
Arrow Electronics | 43 | 45 | 59 |  
IBM | 41 | 41 | 59 |  

### Result
The result presented shows the top 20 companies' counts of orders for 'poster', 'gloss', and 'standard' papers, organized by company name. 
The number of orders for the 'poster' paper is lower in comparison to the other two papers. The 'gloss' paper, on the other hand, seems to have 
a slightly higher count of orders. However, it is the 'standard' paper that appears to be in high demand, based on the order count with EOG 
resources topping the order list followed by Leucadia National.

### Insights : 
•	The query aims to identify the top 20 frequent buyers and customers, indicating their loyalty to the business. 

•	Understanding these customers' product preferences (standard, gloss, and poster) can help tailor marketing efforts to cater to their interests.

•	By focusing rewards and incentives on these top customers, the company can foster customer retention and loyalty. 

•	Analyzing why these customers prefer one product over another can provide valuable insights into consumer behavior, aiding in the design of 
        effective sales and marketing strategies to attract and retain similar customers.

--- 

2. ### What is the total sales made and their distribution across each documented year? 

### Steps : 
•	Retrieve the year from the date.

•	Sum and present the total sales made to show their distribution based on each documented year.

```sql 
SELECT
    DATE_PART('year', occurred_at) AS Year,
    SUM(total_amt_usd) AS Total_Sales
FROM
    orders
GROUP BY 
   Year
ORDER BY 
    Year;
``` 
### Output
Year | Total_Sales
-- | --
2017 | 78151.43
2013 | 377331
2015 | 5752005
2016 | 12864918
2014 | 4069107

### Result 
Revenue increased significantly from $377K in 2013 to $12.8M in 2016 due to successful financial strategies and business expansions but 
dropped to $78K in 2017 - could be an exceptional circumstance or financial restructuring. 

### Insights : 
•	The query aims to provide an overview of total sales and how they are distributed across documented years.

•	Analyzing sales data over time can help identify trends, patterns, and potential growth or decline in sales for different years.

•	This information can be valuable for making informed business decisions, such as focusing on years with high sales for strategic planning 
        or addressing issues in years with lower sales. 

--- 

3. ### What is the total monthly revenue per year?   

### Steps : 
•	Retrieve the year from the date. 

•	Retrieve the month from the date.
 
•	Sum the total sales generated to determine if there are busy seasons or peak periods within the company's operation. 

```sql 
SELECT
    DATE_PART('Year', occurred_at) AS Year,
    DATE_PART('month', occurred_at) AS Month,
 SUM(total_amt_usd) AS Total_Sales
FROM orders
GROUP BY
    Year, Month
ORDER BY 
     Year; 
```
### Output 
Year | Month | Total_Sales
-- | -- | --
2013 | 12 | 377331
2014 | 1 | 286140.3
2014 | 2 | 349721.3
2014 | 3 | 341512.3
2014 | 4 | 344894
2014 | 5 | 319210.4
2014 | 6 | 297655.7
2014 | 7 | 289128.2
2014 | 8 | 366685.4
2014 | 9 | 299968.4
2014 | 10 | 495333.6
2014 | 11 | 311893.9
2014 | 12 | 366963.1
2015 | 1 | 347804.3
2015 | 2 | 333688
2015 | 3 | 519403.4
2015 | 4 | 451753.6
2015 | 5 | 390830.8
2015 | 6 | 420906.1
2015 | 7 | 461895.5
2015 | 8 | 463754.3
2015 | 9 | 510848.6
2015 | 10 | 554190.8
2015 | 11 | 682094.3
2015 | 12 | 614835.2
2016 | 1 | 625565.9
2016 | 2 | 629207.3
2016 | 3 | 799072.2
2016 | 4 | 765390.2
2016 | 5 | 827041
2016 | 6 | 1152557
2016 | 7 | 1227707
2016 | 8 | 1087667
2016 | 9 | 1206400
2016 | 10 | 1377982
2016 | 11 | 1396046
2016 | 12 | 1770283
2017 | 1 | 78151.43

### Result
The output displayed the monthly sum of orders to determine busy seasons or peak periods of the company. After analyzing the previous query,
it was discovered that the low sales recorded for the year 2017 were a result of only having sales data for the month of January that year, this query shows that. 

### Insights : 
•	The query aims to provide insights into the company's revenue patterns over time, specifically by breaking it down into monthly totals per year.

•	By calculating the average order amount on a monthly basis, it becomes possible to identify whether certain months experience higher order values, 
         potentially indicating busy seasons or peak periods.

•	Understanding these patterns can help the company allocate resources effectively, plan marketing campaigns, and optimize its operations during peak times.

---

4. ### What revenue is generated per company?  

### Steps : 
•	Retrieve the Name for each company.

•	Sum the total sales generated by each company. 

•	Identify and distinguish between high-performing and low-performing companies by presenting them in descending order.  

```sql 
SELECT 
    a.Name, 
    SUM(o.total_amt_usd) AS Total_Sales
FROM 
    orders AS o
JOIN 
    accounts AS a 
ON 
    o.account_id = a.id
GROUP BY 
    a.Name
ORDER BY 
    Total_Sales DESC;
``` 

### Output 
'for the purpose of the output, it is limited to top 30 and least 5 companies
Name | Total_Sales
-- | --
EOG Resources | 382873.3
Mosaic | 345618.6
IBM | 326819.5
General Dynamics | 300694.8
Republic Services | 293861.1
Leucadia National | 291047.3
Arrow Electronics | 281018.4
Sysco | 278575.6
Supervalu | 275288.3
Archer Daniels Midland | 272672.8
Western Digital | 269155.3
Pacific Life | 255319.2
DISH Network | 253635.8
Fluor | 251299.8
United States Steel | 244696
Avis Budget Group | 241711.3
Centene | 238962.5
Aetna | 237781.3
United Technologies | 235051.1
Freddie Mac | 234225.7
Core-Mark Holding | 231857
KKR | 217473.9
Disney | 205887
Wells Fargo | 200174.7
Bristol-Myers Squibb | 188457.5
Goodyear Tire & Rubber | 185463.8
Jarden | 179761.4
Intel | 167717.4
CHS | 167271.8
Best Buy | 163887.4
   Least Preforming five 
Bed Bath & Beyond | 1069.64
Deere | 1036.57
Level 3 Communications | 881.73
Delta Air Lines | 859.64
Nike | 390.25

### Result 
The output showed the total revenue generated per company, with a focus on identifying important classes of customers, including 
high or low-performing companies. Some of the high-performing companies include Pacific Life, Core-Mark Holdings, EOG Resources, 
Dish Networks, and Fidelity Nationals Financial. The bottom five least-performing companies include; Nike, Delta Air Lines, Level 3 
Communications, Deere, and Bed Bath & Beyond.

### Insights : 
•	The query aims to assess and quantify the patronage rate of companies, which can be a key metric for understanding their performance.

•	By categorizing companies into different classes based on their patronage rate, it becomes possible to identify those who are excelling 
         (high-performing) and those who may require attention or improvement (low-performing).

•	These insights can guide decision-making, allowing for targeted strategies to retain or improve relationships with key customers and address issues with underperforming ones.

---

5. ### What is the yearly percentage of each revenue line (poster, gloss, and standard) in relation to the total revenue in USD?  

### Steps : 
•	Retrieve the year from the date.

•	Calculate the percentage of revenue contributed by each revenue line (poster, gloss, and standard) 
        in relation to the total revenue for each year. 

```sql 
SELECT 
date_part('Year', occurred_at) AS Year,
ROUND(SUM(standard_amt_usd)/SUM(total_amt_usd)*100,2)AS Standard_Paper,
	ROUND(SUM(poster_amt_usd)/SUM(total_amt_usd)*100,2) AS Poster_Paper,
	ROUND(SUM(gloss_amt_usd)/SUM(total_amt_usd)*100,2) AS  Gloss_Paper
FROM 
orders
GROUP BY 
Year;
``` 

### Output 
Year | Standard_Paper | Poster_Paper | Gloss_Paper
-- | -- | -- | --
2013 | 35.12 | 23.82 | 41.06
2014 | 43.41 | 24.96 | 31.63
2015 | 41.45 | 24.27 | 34.28
2016 | 41.69 | 26.07 | 32.24
2017 | 33.74 | 26.08 | 40.18

### Result
Over the course of 5 years, the total amounts for standard paper ranged from 33.74 to 43.41, poster paper ranged from 23.82 to 26.08, and gloss paper
ranged from 31.63 to 41.06. The analysis revealed that standard paper had the highest yearly percentage, followed by gloss paper and poster paper coming in last. 

### Insights : 
•	The query aims to provide a breakdown of revenue contribution by different revenue lines (products) on a yearly basis.

•	By calculating the percentage of each revenue line to the total revenue, it becomes possible to identify which product category generates the highest income relative to the others.

•	This information is valuable for strategic decision-making, allowing the company to focus resources and marketing efforts on the most profitable product lines. 

--- 

6. ### What are the companies with the highest average number of orders? 

### Steps : 
•	The SQL query initiates a Common Table Expression (CTE) named Sub1 to compute the total order amount (order_total) for each account by combining data
         from the "orders" and "accounts" tables using the account_id as the linking key. 

•	Aggregate Order Amounts: Within Sub1, the SUM function is employed to aggregate the total_amt_usd for each account, resulting in the total order amount for
        each account. Group by Account ID. 

•	The GROUP BY clause is used in Sub1 to group the results based on the account_id, ensuring that each account's total order amount is distinct. Reference the CTE in Main Query.

•	The main query references the CTE (Sub1) and joins it with the "accounts" table based on the account_id. Calculate Average Order Total.

•	The main query computes the average order total (avg_order) for each account by applying the AVG function to the order_total calculated in Sub1. 

•	Group Results by Account ID and Name: Lastly, the results are grouped by both the account ID and account name, presenting the average order total for each account,
        along with their respective names. 

```sql  
WITH Sub1 AS (
    SELECT orders.account_id, SUM(orders.total) AS Total_Sales
    FROM orders
    JOIN accounts ON accounts.id = orders.account_id
    GROUP BY orders.account_id
)
SELECT accounts.Name, ROUND(AVG(total_sales), 2) AS Avg_Sales
FROM Sub1
JOIN accounts ON accounts.id = Sub1.account_id
GROUP BY accounts.id, accounts.name
ORDER BY Avg_Sales DESC;
```

### Output 
The output shows the Top 20 
Name | Avg_Sales
-- | --
EOG Resources | 56410
Mosaic | 49246
IBM | 47506
Core-Mark Holding | 44750
General Dynamics | 43730
Leucadia National | 42358
Arrow Electronics | 40904
Republic Services | 40833
Sysco | 40535
Supervalu | 40412
Archer Daniels Midland | 39863
DISH Network | 39570
Western Digital | 39187
Fluor | 36887
United States Steel | 36603
Avis Budget Group | 35750
Centene | 35358
United Technologies | 34963
Aetna | 34627

### Results 
EOG Resources still ranks first in overall company orders, followed by Mosaic, IBM, Core-Mark Holding, and General Dynamics. However, these companies differ 
in terms of revenue generation. 

### Insights : 
This query was necessary as it was noticed that the number of orders was not directly proportional to the revenue generated by a company.

•	By combining both order counts, the query helps pinpoint the most valuable customers or partners to the business.

•	These insights can be used to nurture and strengthen relationships with high-value clients or prioritize strategies to maintain and enhance such partnerships 
        for sustained revenue growth.

--- 

7. ### Which channel is the best for acquiring customers? 

### Steps : 
•	Summing up the total orders made.

•	 Retrieve the channel. 

```sql 
SELECT 
Channel,
sum(o.total) AS Total_Orders
FROM orders AS o
JOIN accounts AS a
ON o.account_id = a.id
JOIN web_events as w
ON w.account_id = a.id
GROUP BY 
   Channel; 
ORDER BY 
   Total_Orders DESC;
```

### Output
Channel | Total_orders
-- | --
twitter | 8264526
adwords | 15891377
organic | 16630210
facebook | 16682255
banner | 7916299
direct | 1.02E+08

### Result
The "Direct" channel has the highest interaction count, showing that it is particularly effective, followed by Facebook. Meanwhile, the Twitter channel with the banner has the lowest engagement.

### Insights : 
•	The query's primary objective is to identify the channel or marketing method that yields the best results in terms of acquiring customers.

•	By evaluating the effectiveness of various acquisition channels, businesses can allocate resources more efficiently to strategies that bring in the highest number of new customers.

•	The insights gained from this query can inform marketing and acquisition strategies, helping the company focus on tactics that provide the best return on investment for customer 
        acquisition.

---

8. ### What is the total signups received via different channels on an hourly basis? 

### Steps : 
•	Group the signup data by hour.

•	Calculate the total number of signups for each hour.

•	Segment and identify the channels through which these signups occurred.

•	Summarize the total signups received via different channels for each hour.  

```sql 
SELECT
    DATE_PART('hour', o.occurred_at) AS Hour_of_Signup,
    Channel,
    COUNT(*) AS Total_Signups
FROM orders as o
JOIN accounts as a
ON o.account_id = a.id
JOIN web_events as w
ON w.account_id = a.id
GROUP BY 
   DATE_PART('hour', o.occurred_at), channel
ORDER BY 
    Hour_of_Signup, Channel;
``` 
### Output
Hour_of_Signup | Channel | Total_Signups
-- | -- | --
0 | adwords | 1256
0 | banner | 584
0 | direct | 7664
0 | facebook | 1354
0 | organic | 1230
0 | twitter | 681
1 | adwords | 888
1 | banner | 447
1 | direct | 6229
1 | facebook | 908
1 | organic | 907
1 | twitter | 465
2 | adwords | 1289
2 | banner | 689
2 | direct | 8239
2 | facebook | 1393
2 | organic | 1454
2 | twitter | 649
3 | adwords | 1405
3 | banner | 717
3 | direct | 8909
3 | facebook | 1515
3 | organic | 1516
3 | twitter | 725
4 | adwords | 1342
4 | banner | 752
4 | direct | 9522
4 | facebook | 1489
4 | organic | 1546
4 | twitter | 743
5 | adwords | 1184
5 | banner | 584
5 | direct | 7051
5 | facebook | 1222
5 | organic | 1284
5 | twitter | 617
6 | adwords | 1302
6 | banner | 633
6 | direct | 8015
6 | facebook | 1360
6 | organic | 1317
6 | twitter | 652
7 | adwords | 1316
7 | banner | 644
7 | direct | 8089
7 | facebook | 1302
7 | organic | 1261
7 | twitter | 645
8 | adwords | 1184
8 | banner | 681
8 | direct | 7777
8 | facebook | 1291
8 | organic | 1294
8 | twitter | 662
9 | adwords | 1539
9 | banner | 701
9 | direct | 9604
9 | facebook | 1627
9 | organic | 1643
9 | twitter | 845
10 | adwords | 1171
10 | banner | 611
10 | direct | 7489
10 | facebook | 1281
10 | organic | 1191
10 | twitter | 668
11 | adwords | 1217
11 | banner | 726
11 | direct | 8343
11 | facebook | 1367
11 | organic | 1403
11 | twitter | 609
12 | adwords | 1226
12 | banner | 688
12 | direct | 8123
12 | facebook | 1407
12 | organic | 1365
12 | twitter | 711
13 | adwords | 1428
13 | banner | 702
13 | direct | 9356
13 | facebook | 1532
13 | organic | 1451
13 | twitter | 720
14 | adwords | 960
14 | banner | 502
14 | direct | 6500
14 | facebook | 1014
14 | organic | 1010
14 | twitter | 478
15 | adwords | 1111
15 | banner | 588
15 | direct | 7404
15 | facebook | 1139
15 | organic | 1195
15 | twitter | 535
16 | adwords | 1298
16 | banner | 677
16 | direct | 8633
16 | facebook | 1379
16 | organic | 1390
16 | twitter | 679
17 | adwords | 1304
17 | banner | 678
17 | direct | 8255
17 | facebook | 1389
17 | organic | 1396
17 | twitter | 693
18 | adwords | 1128
18 | banner | 556
18 | direct | 7308
18 | facebook | 1210
18 | organic | 1222
18 | twitter | 589
19 | adwords | 1316
19 | banner | 694
19 | direct | 8269
19 | facebook | 1371
19 | organic | 1403
19 | twitter | 671
20 | adwords | 1160
20 | banner | 593
20 | direct | 7915
20 | facebook | 1260
20 | organic | 1273
20 | twitter | 623
21 | adwords | 1557
21 | banner | 803
21 | direct | 9769
21 | facebook | 1594
21 | organic | 1571
21 | twitter | 854
22 | adwords | 1328
22 | banner | 686
22 | direct | 8441
22 | facebook | 1472
22 | organic | 1526
22 | twitter | 778
23 | adwords | 1233
23 | banner | 698
23 | direct | 8332
23 | facebook | 1375
23 | organic | 1380
23 | twitter | 700

### Result 
The result showed that the level of activity was relatively high during the night and early hours of the day as compared to the daytime hours. 
The findings revealed that the highest level of activity was recorded around 10 pm, followed by 4 am while the activity level during the afternoon
and 9 am was relatively high. These results provide insights into the patterns of activity during different times of the day.  

### Insights : 
•	The query aims to provide a detailed breakdown of signups received through various channels, allowing the company to assess channel effectiveness. 

•	By analyzing signups per hour, businesses can identify peak times and adjust their marketing strategies accordingly. 

•	These insights can help optimize resource allocation and refine marketing efforts to maximize signups through the most effective channels and during high-traffic hours. 

---

9. ### What is the performance of sales representatives who have both the highest number of orders and the highest revenue generated?

### Steps : 
•	Retrieve the sales rep's name. 

•	Retrieve and return the sum of the total orders made by each sales rep. 

•	Retrieve and return the total revenue generated made by each sales rep. 

•	Grouped by the sales rep and ordered descending by the total revenue. 

```sql 
SELECT 
s.name As sales_reps, 
COUNT(o.total) as Total_Orders, 
	SUM(total_amt_usd) as Total_Revenue
FROM sales_reps as s
JOIN accounts as a
ON s.id = a.sales_rep_id
JOIN orders as o
ON a.id = o.account_id
GROUP BY 
s.Name
ORDER BY 
Total_Revenue DESC
``` 
### Output
The output shows the Top 20
Sales_Reps | Total_Orders | Total_Revenue
-- | -- | --
Earlie Schleusner | 335 | 1098138
Tia Amato | 267 | 1010691
Vernita Plump | 299 | 934212.9
Georgianna Chisholm | 256 | 886244.1
Arica Stoltzfus | 186 | 810353.3
Dorotha Seawell | 208 | 766935
Nelle Meaux | 241 | 749076.2
Sibyl Lauria | 193 | 722084.3
Maren Musto | 224 | 702697.3
Brandie Riva | 167 | 675917.6
Charles Bidwell | 205 | 675637.2
Elwood Shutt | 191 | 662500.2
Maryanna Fiorentino | 204 | 655954.7
Moon Torian | 250 | 650393.5
Hilma Busick | 191 | 622808
Dawna Agnew | 116 | 604519.4
Calvin Ollison | 199 | 594516.1
Cliff Meints | 151 | 556105.3
Gianna Dossey | 184 | 550973
Michel Averette | 173 | 523977.1
-- | -- | --

### Result 
Schleusner has generated the highest revenue of approximately $1,098,138 and received 335 orders, making the top-performing sales representative. 
The output shows other high-performing representatives, including Tia Amato, Venita Plump, Georgianna Chisholm, and Arica Stoltzfus.

### Insights :
•	The query's objective is to identify and highlight the sales representatives who excel in terms of both order volume and revenue generation.

•	By considering both order count and revenue criteria, the query helps pinpoint the top-performing sales representatives who significantly contribute to the company's success.

•	These insights can be used for recognizing and rewarding top-performing sales representatives, as well as for identifying and sharing best practices that lead to high-order counts and 
        revenue among the sales team. 

---

10. ### What region(s) that have generated the highest total revenue? 

### Steps : 
•	Retrieve the region name.

•	Calculate the total revenue generated for each region by each revenue line (poster, gloss, and standard) in relation to the region.

```sql  
SELECT 
region.Name,   
        	sum(standard_amt_usd) as Total_Standard_Amt,
	sum(gloss_amt_usd) as Total_Gloss_Amt,
	sum(poster_amt_usd) as Poster_Amt
FROM sales_reps
JOIN region
ON sales_reps.region_id = region.id
JOIN accounts
ON accounts.sales_rep_id = sales_reps.id
JOIN orders
ON orders.account_id = accounts.id
GROUP BY 
    region.id, region.Name
ORDER BY
   region.id
``` 
### Output 
id | Name | Total_Standard_Amt | Total_Gloss_Amt | Poster_Amt
-- | -- | -- | -- | --
1 | Northeast | 3227886 | 2634076 | 1882443 |  
2 | Midwest | 1308553 | 1027913 | 677021.2 |  
3 | Southeast | 2824629 | 2068453 | 1565414 |  
4 | West | 2311278 | 1862718 | 1751127 |  

### Result
The Northeast region showed the most impressive performance in all three product categories with the highest revenue generated, 
followed by the Southeast region with a slightly lower revenue, and then the West region. On the other hand, the Midwest region 
experienced the lowest revenue generation with the least favorable outcome among all. 

### Insights : 
•	The query aims to identify and highlight the regions that contribute the most to the company's revenue.

•	By analyzing the total revenue by region, businesses can prioritize regions for further expansion or allocate resources to regions with the highest revenue potential.

--- 

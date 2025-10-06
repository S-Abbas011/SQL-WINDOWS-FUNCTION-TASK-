# SQL-WINDOWS-FUNCTION-TASK-
I've solved 20 windows function problem 

### WINDOWS FUNCTION
### Row Number - total 21 task

#### 1. Assign a unique row number to each sale in the Sales
table ordered by OrderDate.
```SQL
select sales,
	row_number() over (order by order_date )
from sales 
```

### 2.Display the top 5 most recent orders for each customer using
###                     ROW_NUMBER().
```SQL
Select order_id, 
row_number() over(partition by customer_id
	order by order_date desc)
	from sales 
	limit 5 
```

### 3.Assign row numbers to each product within its category based on total sales amount.
```SQL
Select 
	p.product_name, sum(s.sales) as total_sales,
	row_number() over(partition by p.category
	order by p.product_name)
from product as p
join sales as s 
on p.product_id = s.product_id 
group by product_name,category
```

### 4.For each region, assign a row number to customers based on their total purchase value (highest to lowest).'''
```SQL
select c.region, sum(s.sales) as total_sales,
row_number() over(order by sum(s.sales) desc)
from customer as c
join sales as s 
on c.customer_id = s.customer_id 
group by region
```

#### 5.List the first order made by each customer (using ROW_NUMBER() inside a subquery).'''
```SQL
Select order_id , customer_id
from (select order_id,customer_id,
row_number() over(partition by customer_id
order by order_id) as rn
from sales)
where rn = 1
```

### 6.Generate row numbers for all transactions and display only rows 51 to 100 (for pagination).

```SQL
select * from(
select order_date,
row_number() over(order by order_date) as rn
from sales )
where rn between 51 and 100
```

--7 THERE IS NO JOINING DATE OF THE EMPLOYEE

###  						Rank Number()
### 8.Rank products in each category based on total quantity sold.'''
```SQL
select p.product_name, p.category, 
sum(s.quantity) as total_quantity,
rank() over(partition by p.category order by sum(s.quantity))
from product as p 
join sales as s 
on p.product_id = s.product_id
group by p.product_name, p.category
```

### 9.Rank customers within each region according to their total purchase amount.'''

```SQL
Select c.customer_name, c.region, sum(s.sales) as total_sales,
rank() over(partition by c.region order by sum(s.sales))
from customer as c 
join sales as s
on c.customer_id = s.customer_id
group by c.customer_name, c.region
```

### 10.Rank sales representatives based on their monthly sales performance.'''
```SQL
select order_line,
sum(sales),
extract(month from order_date) as month,
rank() over(partition by extract(month from order_date)
order by sum(sales))
from sales
group by order_line
order by month desc
```
### 11.Show top 3 ranked products by profit in each category using RANK().'''
```SQL
select p.product_name, p.category , s.profit, 
rank() over(partition by p.category order by s.profit desc)
from product as p 
join sales as s 
on p.product_id = s.product_id
limit 5 
```

### 12.Rank stores based on total revenue — note how ties affect the ranking sequence.'''
###  "*don't have stores table but i've done this question using order_id"
```SQL
select order_id , sum(sales),
rank() over(order by sum(sales))
from sales 
group by order_id
```

### 13.For each month, rank the top-performing product by total sales amount.'''
```SQL
select p.product_id, extract(month from s.order_date) as month_no,
sum(s.sales) total_sales,
rank() over(partition by extract(month from s.order_date)
order by sum(s.sales))
from product as p 
join sales as s
on p.product_id = s.product_id
group by p.product_id,s.order_date
order by month_no desc
```
### 14.Rank customers based on the number of orders placed in 2024.'''
```SQL
Select c.customer_name, count(s.order_id) total_order,
extract(year from s.order_date) as year,
rank() over(order by count(s.order_id))
from customer as c 
join sales as s 
on c.customer_id = s.customer_id
where extract(year from s.order_date) = 2017 
group by customer_name,s.order_date
order by total_order desc
````
###							 Dense_rank()

### 15.	Use DENSE_RANK() to find products with the same total sales having the same rank in each category.
```SQL
with SAME_TOTAL AS (Select p.product_name , sum(s.sales), p.category,
dense_rank() over(order by sum(s.sales)) as dense_rank
from product as p
join sales as s 
on p.product_id = s.product_id 
group by p.product_name,p.category 
)
select product_name, category , dense_rank
from SAME_TOTAL 

```

### 16.Rank customers based on their total spending using DENSE_RANK() — observe how it differs from RANK().'''
```SQL
Select c.customer_name, sum(s.sales),
dense_rank() over(order by sum(s.sales) desc) as total_spending_rank
from customer as c
join sales as s 
on c.customer_id = s.customer_id 
group by c.customer_name
 ```
### 17.	Find the top 3 product categories with the highest total revenue using DENSE_RANK().'''
```SQL
with top_3_product as (
select p.product_name , p.category, sum(s.sales) as total_sales,
dense_rank() over(partition by category order by sum(s.sales))
from product as p
join sales as s
on p.product_id = s.product_id 
group by p.product_name, p.category
)
select product_name, category , total_sales
from top_3_product
order by total_sales desc
limit 3 
```
### 18.In each region, assign a dense rank to sales reps based on their sales target achievement.'''
-- There is no any target table in the database

### 19.Identify the top 2 months with the highest profit per region using DENSE_RANK().'''
```SQL
with two_months as (
Select extract(month from order_date) as Month,
sum(profit) as TotaL_profit,
dense_rank() over(order by extract(month from order_date))
from sales 
group by month
)
select month, total_profit 
from two_months
order by total_profit desc
limit 2
```
### 20.	Rank product subcategories by profit margin using DENSE_RANK() and filter the top 5.'''
```SQL
with Profit_margin as (
Select p.sub_category, sum(s.sales) as Total_sales,
sum(s.profit) as Total_profit,
dense_rank() over(partition by p.sub_category order by sum(s.sales)) 
from product as p 
join sales as s 
on p.product_id = s.product_id  
group by p.sub_category
)
select sub_category, Total_profit / Total_sales as Margin 
from profit_margin 
order by margin desc
limit 5 
```

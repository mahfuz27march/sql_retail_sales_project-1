# sql_retail_sales_project-1
```
create table retail_sales(

	transactions_id int primary key,
	sale_date date,
	sale_time time,
	customer_id int,
	gender varchar(15),
	age int,
	category varchar(15),
	quantity int,
	price_per_unit float,
	cogs float,
	total_sale float
	);
select * from retail_sales
where
	transactions_id is null
	or
	sale_date is null
	or
	sale_time is null
	or
	customer_id is null
	or
	gender is null
	or
	age is null
	or 
	category is null
	or 
	quantity is null
	or
	price_per_unit is null
	or
	cogs is null
	or
	total_sale is null;
---------------------------------------	
select * from retail_sales
where sale_date='2022-11-05'
------------------------------------
select * from retail_sales
where 
	category='Clothing'
	and
	to_char(sale_date,'yyyy-mm')='2022-11'
	and
	quantity>=4
---------------------------
select 
	category,
	sum(total_sale)
from retail_sales 
group by 1
--------------------
select category, round(avg(age)) from retail_sales
	where category='Beauty' group by category
------------------------
select 
	gender,
	category,
	count(*)
from retail_sales
	group by gender,category
--------------------------------------
select 
year,
month,
avg_sale
from
	(select 
		extract(year from sale_date) as year,
		extract(month from sale_date) as month,
		avg(total_sale) as avg_sale,
		rank() over(partition by extract(year from sale_date) order by avg(total_sale)desc)as rank
	from retail_sales
	group by 1,2 
	--order by 1,3 desc
	) as t1
where rank=1

-- using CTE--
with avg_rank
as (
select 
	extract(year from sale_date) as year,
	extract(month from sale_date) as month,
	avg(total_sale) as avg_sale,	
	rank() over(partition by extract(year from sale_date) order by avg(total_sale)desc)as rank
from retail_sales
group by 1,2
)
select 
	year,
	month,
	avg_sale
from avg_rank
where rank=1
	
/*with t1 as(
	select 
		extract(year from sale_date) as year,
		extract(month from sale_date) as month,
		avg(total_sale) as avg_sale,
		rank() over(partition by extract(year from sale_date) order by avg(total_sale)desc)as rank
	from retail_sales
	group by 1,2
		)
select 
	t1.year,
	t1.month,
	t1.avg_sale,
	avg(total_sale) as avg_sale
	from retail_sales r
	join t1 on r.avg_sale=t1.avg_sale
	group by 1,2;
	--where t1.rank=1;
*/
--------------------------------------------
select 
	customer_id,
	sum(total_sale) as total_sales
from retail_sales
group by 1
order by 2 desc
limit 5
---------------------------------
select 
	category,
	count(distinct customer_id) 
from retail_sales	
group by 1
---------------------------------
select * from retail_sales

with hourly_sale
as (
select *,
	case
		when extract(hour from sale_time)<12 then 'Morning'
		when extract(hour from sale_time) between 12 and 16 then 'Afternoon'
		else 'Evening'
	end as shift
from retail_sales	
)
select 
	shift,
	count(*) as total_orders
from hourly_sale
```
group by shift;
-------------------------------------

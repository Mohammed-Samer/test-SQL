-- create database 
create database store

-- Drop database
Drop database store 
 
-- create table 
create table product ( IDproduct int primary key ,
serviceDate date ,
Category nvarchar(10), 
SunCategory nvarchar(10) ,
price int ,
cost int ,
IDempolyee int ,
foreign key (IDempolyee) references empolyee(IDempolyee) )

create table empolyee ( IDempolyee int primary key ,
empolyee nvarchar(10) unique ,
salary int ) 
-------------------------------------------------------------------------------------
--rename column 
exec SP_rename 'product.SunCategory' , 'SubCategory'
-------------------------------------------------------------------------------------
-- insert data in the both table 
insert into empolyee values ( 1,'mohamed',2000)
insert into empolyee values ( 2,'ahmed',2000)
insert into empolyee values ( 3,'moustafa',2000)
insert into empolyee values ( 4,'mahmoud',2000)
insert into empolyee values ( 5,'taha',2000)
insert into empolyee values ( 6,'ibrahim',2000)
insert into empolyee values ( 7,'youssef',Null)

insert into product values ( 1,'2018-12-3','clothing','tshirt',150,100,1) 
insert into product values ( 2,'2019-1-3','device','tv',1500,1400,1) 
insert into product values ( 3,'2018-12-3','clothing','tshirt',150,100,2) 
insert into product values ( 4,'2019-1-3','device','tv',1500,1400,2) 
insert into product values ( 5,'2018-12-3','clothing','tshirt',150,100,3) 
insert into product values ( 6,'2019-1-3','device','tv',1500,1400,3) 
insert into product values ( 7,'2018-12-3','clothing','tshirt',150,100,4) 
insert into product values ( 8,'2019-1-3','device','tv',1500,1400,4) 
insert into product values ( 9,'2018-12-3','clothing','tshirt',150,100,5) 
insert into product values ( 10,'2019-1-3','device','tv',1500,1400,5) 
insert into product values ( 11,'2018-12-3','clothing','tshirt',150,100,6) 
insert into product values ( 12,'2019-1-3','device','tv',1500,1400,6) 
insert into product values ( 13,'2019-1-3',null,null,1500,1400,6) 
insert into product values ( 14,'2018-12-3','clothing','tshirt',150,100,7) 
insert into product values ( 15,'2019-1-3','device','tv',1500,1400,7) 

-- select statement
select * from product 
select count(category) from product
select top 4 IDempolyee , price from product
select avg(salary) from empolyee
select category , price from product where category like 'd%'
select upper(empolyee) , salary from empolyee 
select * from product where price > 1000
select left(empolyee,2) from empolyee
select right (empolyee,3) from empolyee
select replace(empolyee,'mohamed','nouh') from empolyee 
select empolyee , salary from empolyee where salary > 1500 and empolyee = 'ahmed'
select getdate()
select category,datename(year,serviceDate) from product
select month(serviceDate) from product
--------------------------------------------------------------------------------------
-- run two column 
select category , price from product
-- run total price with 'Group by' & 'order by '
select category , sum(price) as total from product group by category order by total desc
-- delete row  
delete from product where IDproduct = 12 
-- update 
update empolyee set empolyee = 'mousa' where empolyee = 'ibrahim' 
-----------------------------------------------------------------------------------------
-- join 
select category , empolyee , price , cost , salary from product
join empolyee on product.IDempolyee = empolyee.IDempolyee 
-- left join 
select category , empolyee , price , cost , salary from product
left join empolyee on product.IDempolyee = empolyee.IDempolyee 
-- right join
select category , empolyee , price , cost , salary  from product
right join empolyee on product.IDempolyee = empolyee.IDempolyee 
-- full outer join
select category , empolyee , price , cost , salary  from product
full outer join empolyee on product.IDempolyee = empolyee.IDempolyee 
--------------------------------------------------------------------------------------------
-- union 
select * from product union select * from product 
select * from product intersect select * from product 
select * from product except select * from product

-- create procedure
create procedure inserting 
@IDempolyee int ,
@empolyee nvarchar(10) ,
@salary int 
AS
BEGIN
	insert into empolyee (IDempolyee,empolyee,salary)  values (@IDempolyee,@empolyee,@salary) 
END
GO
exec inserting 8,'adam',2000
-----------------------------------------------------------------------------------------
create procedure calculate 
@category nvarchar(10) 
AS
BEGIN
	 select sum(price) from product where category =@category 
END
GO

exec calculate 'clothing'

-------------------------------------------------------------------------------------------
--if with procedure 
alter procedure iff
@category nvarchar(10)
AS
BEGIN 
	declare @x int
	declare @y int
	select @x=sum(price) , @y=sum(cost) from product where category = @category
	if @x > 5000 print 'total price bigger than 5000'
	else print 'total price less than 5000 '

END
GO

exec iff 'device'

--------------------------------------------------------------------------------------------

-- create function 
create function scalar_func 
(
@x int ,
@y int
)
Returns int
AS
BEGIN
	return @x - @y
END
Go

select category,dbo.scalar_func(price,cost) from product
-------------------------------------------------------------------------------------------
create function inline_func
(
	@category nvarchar(10)
) 
Returns table
AS
Return(
		SELECT        dbo.product.Category, dbo.product.[product.SubCategory], dbo.product.price, dbo.product.cost, dbo.empolyee.empolyee, dbo.empolyee.salary
		FROM            dbo.empolyee INNER JOIN
                         dbo.product ON dbo.empolyee.IDempolyee = dbo.product.IDempolyee
		where category=@category
)
Go

select * from dbo.inline_func('device')

---------------------------------------------------------------------------------------------
create view view_1
AS
SELECT        dbo.product.Category, dbo.product.[product.SubCategory], dbo.product.price, dbo.product.cost, dbo.empolyee.empolyee, dbo.empolyee.salary
FROM            dbo.empolyee INNER JOIN
                         dbo.product ON dbo.empolyee.IDempolyee = dbo.product.IDempolyee

select * from view_1
select empolyee , salary from 
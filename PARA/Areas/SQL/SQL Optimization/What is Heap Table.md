[# What is heap table | Full table scan](https://www.youtube.com/watch?v=VzpVrlmoPvU&list=PL6n9fhu94yhXg5A0Fl3CQAo1PbOcRPjd0&index=3)
[Materials](https://www.pragimtech.com/blog/sql-optimization/what-is-heap-table/)

# What is heap table | Full table scan

![what is heap table](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/what-is-heap-table.jpg)

In this video we will discuss

1. What is a heap table, how to create and when to use it.
2. Along the way, we will understand the concept of Table Scan as well.
3. We will also look at a simple example in action, where a full table scan is better from performance standpoint than using a table index.
4. Finally, we will also discuss how to force the database query engine to use a specific index to find the data we are looking for.
5. Understanding all these concept is very important when you are tuning SQL queries for better performance. 

---
## What is a heap table

Let's understand this with an example. Consider the following `Employees` table. 

![how is data physically stored in a database](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/how-is-data-physically-stored-in-a-database.jpg)

`EmployeeId` is the primary key, so by default a clusterd index on this column is created. This means the data rows that are physically stored in the Employees table are sorted by EmployeeId column.

---
## What if we don't have a clustered index on a table?

Well, it is the clustered index that determines the order in which data rows are physically stored in a table. Without a clustered index, the data rows are not guaranteed to be in any specific order in the table. This kind of a table is called a heap. It may have one or more non-clustered indexes, but if it doesn't have a clustered index, then such a table is called a heap table or just heap. So, in short, a table without a clustered index is called heap.

![what is heap in sql server](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/what-is-heap-in-sql-server.png)

---
## Heap table without non-clustered index

What happens when we select data from a heap. Will the database engine use Index Seek, Index Scan or Table Scan?

Well, it depends. We already know, a heap is a table without clusterd index, but it may have one or more non-clustered indexes. But for this example, let's assume the heap does not have any non-clustered indexes as well.

If there are no non-clusterd indexes on the heap, the search query uses Table Scan to find the data we are looking for. What is a table scan? Well, as the name implies, a table scan, scans every row in the table to find the row we are looking for. Let's look at this in action.

---
## SQL Script to create Gender table

Notice we did not mark any of the columns as primary key and we also did not create a clsustered index explicitly. So the physical order in which data rows are stored in this table is not guaranteed. In a nut shell, this Gender table is a heap. We also did not create any non-clsusterd indexes.

```markup
Create Table Gender
(
	GenderId int,
	GenderName nvarchar(20)
)
Go

Insert into Gender values(1, 'Male')
Insert into Gender values(3, 'Not Specified')
Insert into Gender values(2, 'Female')
```

Use `sp_helpindex` system stored procedure to check if a table has any indexes

```markup
sp_helpindex Gender
```

Now, execute the following query with `Include Actual Execution Plan` turned on

```markup
Select * from Gender where GenderName = 'Male'
```

From the execution plan, it's clear, a table scan is being used to find the data we are looking for.

![sql server execution plan table scan](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/sql-server-execution-plan-table-scan.png)

Notice, from the stats below, `Number of rows read` is 3 and `Actual number of rows for all executions` is 1. So, to get the 1 row we need, SQL server has to read all the 3 rows in the table.

![what is table scan in sql server example](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/what-is-table-scan-in-sql-server-example.png)

Also notice, `Estimated Subtree Cost` is 0.0032853, which is not bad at all from performance standpoint. So, 3 key points to keep in mind here are the following.

1. A table scan is used if there are no indexes to help a query that selects data from a table heap
2. Table scan is not always bad from performance standpoint, especially if you have a table with very a few rows like this Gender table
3. Even if there is an index on a very small table like this Gender table, SQL server might still end up using table scan. This is beacause a table scan provides better performance than using the index. Let's actually prove this last point.

---
## Non-clusterd index on heap table

```markup
Create nonclustered index IX_Gender_GenderName
on Gender(GenderName)
```

Execute the following query with Include Actual Execution Plan turned on

```markup
Select * from Gender where GenderName = 'Male'
```

![sql server execution plan table scan](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/sql-server-execution-plan-table-scan.png)

Though we have a non-clustered index on GenderName column, SQL server is still using Table Scan instead of Index Seek. This is because the database engine knows Table Scan produces better performance than index seek as there are very few rows in the Gender table. So, another key point to keep in mind here is, if the table has very few rows and even if there is an index to help the query, the database engine might still end up performing a full table scan instead of using the index.

---
## Force a query to use an index in sql server

SQL Server query optimizer selects the best execution plan for a query. However, if need be, we can force the query engine to use an index.

```markup
Select * from Gender with (Index(IX_Gender_GenderName))
where GenderName = 'Male'
```

Index Seek is now used instead of Table Scan

![index seek in execution plan](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/index-seek-in-execution-plan.png)

The overall SELECT statement Estimated Subtree Cost is 0.0065704

![index seek in sql server with example](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/index-seek-in-sql-server-with-example.png)

With the table scan, on the other hand, the overall SELECT statement Estimated Subtree Cost is 0.0032853

![table scan even with index](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/table-scan-even-with-index.png)

So, the bottom line is, we have better performance with table scan than using the index. Hence, the query optimizer chose table scan over using the index.

---
## How are data rows stored in the heap

- Data is initially stored in the order in which is the rows are inserted into the heap table.
- However, the Database Engine can move data around in the heap to store the rows efficiently, so the order of data rows in a heap cannot be predicted. 
- To guarantee the order of rows returned from a heap, you must use the ORDER BY clause in your query.

---
## How to create and remove heaps

![how to create heap table in sql server](https://www.pragimtech.com/blog/contribute/article_images/1220210404125459/how-to-create-heap-table-in-sql-server.jpg)

To create a heap, create a table without a clustered index. If a table already has a clustered index, drop the clustered index to return the table to a heap.

You already have a heap and to remove it, simply create a clustered index on the heap.

---
## When to use a heap

- A heap can be used as a staging table for large and unordered insert operations. Because data is inserted without enforcing a strict order, the insert operation is usually faster than the equivalent insert into a table with clustered index.
- The table is small with just a few rows.
- Data is always accessed through nonclustered indexes.
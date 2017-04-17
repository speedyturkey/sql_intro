## SQL Intro

### What is SQL?
SQL is short for Structured Query Language. It is a programming language used for reading data from and writing data to databases.

### What is a database?
A database can be almost anything, from a single spreadsheet saved to your desktop to a complex
network of services spread across the cloud. Usually when we refer to a database we are referring to a "relational database", or more precisely a RDBMS - a Relational Database Management System. There are different types, including Microsoft Access, Microsoft SQL Server, and many more. At Ankura, we generally use an RDBMS called mySQL. In basic terms, a database stores information about various pieces of data that relate to each other in some way - you can think of a database as a way to logically organize information.

### How is a database related to a website or web application?
Many modern websites - including ClaimsOnline - are built "on top" of databases. When you click a link, type a URL, or make a search, the website is actually executing SQL statements behind the scenes to locate, extract, and display the information that you requested.

### What are the basic concepts I need to understand?
One of the most important database concepts is the "table". A table is like a spreadsheet - it is a grid of information structured in a particular way. Rows in a table are called "records", and columns are called "fields" or "attributes". Consider the following simplified table representing Claims.

###### Claims Table

| claim_id | first_name | last_name | claim_status | law_firm |
| ------------ | ---------- | --------- | ------------ | -------- |
| 1001 | Dorothy | Wilson | In Review | Johnson Law Firm
| 1002 | Jane | Smith | In Review | Lopez & Associates
| 1003 | Dawn | Jones | Release Issued | Roy G. Biv, Attorney At Law
| 1004 | Mindy | Brown | Release Review | Johnson Law Firm

It is easy to query this table to get information we are interested in. For example, the following
query would return the rows where Claim Status is "In Review":

~~~
select *
from claims
where claim_status = 'In Review'
~~~

If we just wanted the claim number and last name, we could use this query instead:

~~~
select claim_number, last_name
from claims
where claim_status = 'In Review'
~~~

There are a couple of problems with this simple table. What if we want to store more information about the law firms, such as address or attorney names? There is no place for that data. What if the name of a law firm changes? We would have to update every single claim record associated with that law firm.

The better way to handle this would be to have a separate table for law firms. Consider this:

###### Firms Table

| firm_id | firm_name | firm_location |
| ------- | --------- | ------------- |
| 1 | Johnson Law Firm | Miami, Florida
| 2 | Lopez & Associates | Houston, Texas
| 3 | Roy G. Biv, Attorney at Law | Houston, Texas

We now have a claims table and a firms table. The claims table is where we keep information about claims,
and the firms table is where we keep information about firms.

Notice the field called firm_id. This field is very important - it is called a "primary key" and is used to identify an individual row and to ensure that all rows are unique. Now that we have this field, we can update our claims table as follows:

claim_number | first_name | last_name | claim_status | firm_id |
------------ | ---------- | --------- | ------------ | -------- |
1245 | Dorothy | Wilson | In Review | 1
1246 | Jane | Smith | In Review | 2
1247 | Dawn | Jones | Release Issued | 3
1248 | Mindy | Brown | Release Review | 1

Instead of the firm name, we are showing the firm_id in the claims table. This is called a "foreign key", which means that it points to another table's primary key.

Now we can update any information related to the law firms that may change, or add new information, without needing to change the claims table at all. But now that our data lives in two different places, how can we query it? In order to query again multiple tables, we need to use something called a "join".

Joins

A join is just a way to link data together from two or more different tables. There are several different kinds of joins but each of them usually involves using the primary keys of the tables involved. A query that returns all of the information from our tables would look like this:

~~~
select *
from claims
inner join firms
  on claims.firm_id = firms.firm_id
~~~

The last line of this query is called the "join condition". It means that we would like to return records where the foreign key on the claims table is equal to the primary key on the firms table. This tells the database how our tables are related to each other and makes sure that our results make sense.

If we wanted just the claims belonging to law firms located in Houston, we could write the following query:

~~~
select *
from claims
inner join firms
  on claims.firm_id = firms.firm_id
where
  firms.firm_location = 'Houston, TX'
~~~

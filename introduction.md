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
| 1005 | Jane | McDougall | Release Verified | The Smith Firm

The simplest query we can write to get data from this table looks like this:
~~~
# Query 1
select *
from claims
~~~

Executing this query will return data from the `claims` table. There are no filters, so it will return all of the records (rows) in the table. The query also indicates which fields should be returned: the special operator `*` means "all fields". In English, you might write this query as: "select all fields (columns) from the claims table, with no filters". The following query would return exactly the same results:

~~~
# Query 2
select claim_id, first_name, last_name, claim_status, law_firm
from claims
~~~

The only difference between Query 1 and Query 2 is that Query 2 lists each field individually instead of using `*` - but the results are the same.

If we didn't need all of the fields, we could write this query:

~~~
# Query 3
select law_firm, first_name, last_name
from claims
~~~

Query 3 returns the same number of rows as Query 2, but only the three fields we wanted. Notice that the order of the fields has changed - you can type the fields you want in any order you like.

What if we wanted a query that would only return certain rows? SQL allows us to filter using almost any criteria imaginable using the keyword `where`. Consider this query:

~~~
# Query 4
select *
from claims
where first_name = 'Jane'
~~~

This query returns the following two rows:

| claim_id | first_name | last_name | claim_status | law_firm |
| ------------ | ---------- | --------- | ------------ | -------- |
| 1002 | Jane | Smith | In Review | Lopez & Associates
| 1005 | Jane | McDougall | Release Verified | The Smith Firm

Notice that the name Jane is surrounded by single quotes: `'Jane'`. It is necessary to use single quotes to tell the database that Jane is a string of text, instead of the name of a column, table, or something else.

Here is another example:

~~~
# Query 5
select *
from claims
where claim_status = 'In Review'
~~~

| claim_id | first_name | last_name | claim_status | law_firm |
| ------------ | ---------- | --------- | ------------ | -------- |
| 1001 | Dorothy | Wilson | In Review | Johnson Law Firm
| 1002 | Jane | Smith | In Review | Lopez & Associates

If we just wanted the claim number and last name, we could use this query instead:

~~~
# Query 6
select claim_id, last_name
from claims
where claim_status = 'In Review'
~~~

| claim_id | last_name |
| ------------ | ---------- |
| 1002 | Smith |
| 1005 | McDougall |

You can even filter using more than one criteria in the "where" clause of your query:

~~~
# Query 6
select *
from claims
where
  first_name = 'Jane'
  or claim_status = 'In Review'
~~~

This will return the following three rows, where _either_ of the conditions specified is true. Note that this time, the where clause is spread out over several lines - for the most part, SQL does not care about line breaks. This allows us to write queries that are easy to read.

| claim_id | first_name | last_name | claim_status | law_firm |
| ------------ | ---------- | --------- | ------------ | -------- |
| 1001 | Dorothy | Wilson | In Review | Johnson Law Firm
| 1002 | Jane | Smith | In Review | Lopez & Associates
| 1005 | Jane | McDougall | Release Verified | The Smith Firm

The following query uses the `and` operator instead of `or`. Not surprisingly, this query returns only one row: where _both_ of the conditions specified are true.

~~~
# Query 7
select *
from claims
where
  first_name = 'Jane'
  and claim_status = 'In Review'
~~~

| claim_id | first_name | last_name | claim_status | law_firm |
| ------------ | ---------- | --------- | ------------ | -------- |
| 1002 | Jane | Smith | In Review | Lopez & Associates |

We can also filter using numeric criteria, like in the following query.

~~~
# Query 8
select *
from claims
where
  claim_id > 1002
~~~

This returns the three rows where claim_id is greater than 1002.

| claim_id | first_name | last_name | claim_status | law_firm |
| ------------ | ---------- | --------- | ------------ | -------- |
| 1003 | Dawn | Jones | Release Issued | Roy G. Biv, Attorney At Law
| 1004 | Mindy | Brown | Release Review | Johnson Law Firm
| 1005 | Jane | McDougall | Release Verified | The Smith Firm

There are a couple of problems with our simple  `claims` table. What if we want to store more information about the law firms, such as address or attorney names? There is no place for that data. What if the name of a law firm changes? We would have to update every single claim record associated with that law firm.

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

claim_id | first_name | last_name | claim_status | firm_id |
------------ | ---------- | --------- | ------------ | -------- |
1001 | Dorothy | Wilson | In Review | 1
1002 | Jane | Smith | In Review | 2
1003 | Dawn | Jones | Release Issued | 3
1004 | Mindy | Brown | Release Review | 1

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

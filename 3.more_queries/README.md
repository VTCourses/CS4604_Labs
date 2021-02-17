## Objective
Learn more advanced SQL queries.

## Lab

### Setup
* Connect to a database
   ```
   > sqlite3 lab3.db
   ```

A data set for the collection of cards for [Hearthstone](https://playhearthstone.com/en-us/),
the popular online card game by Blizzard. This data set is freely available from 
[Kaggle](https://www.kaggle.com/jeradrose/hearthstone-cards).


### ORDER BY

The ORDER BY keyword is used to sort the result-set in ascending or descending order.

The ORDER BY keyword sorts the records in ascending order by default. 
To sort the records in descending order, use the DESC keyword. (Ascending [ASC] is the default.)

The syntax is:

```sql
SELECT column1, column2, ...
  FROM table_name
 ORDER BY column1, column2, ... ASC|DESC;
```

**Try these queries:**

```sql
SELECT name FROM basic_cards WHERE type = 'HERO' ORDER BY name;

SELECT name FROM basic_cards WHERE type = 'HERO' ORDER BY name DESC;

SELECT cost, player_class, name FROM basic_cards ORDER BY player_class, cost DESC;
```

Note on the last query we are ordering first by player_class and then by cost _descending_. 
Null values are normally sorted last in SQLite, while Null values are normally sorted first in PostgreSQL.

In PostgreSQL, we have specified "NULLS LAST". By adding the "NULLS LAST" directive, we are telling SQL to put rows having a NULL value at the bottom of the list.
```
SELECT cost, player_class, name FROM basic_cards ORDER BY player_class, cost DESC NULLS LAST;
```

### Aggregate functions

There are several aggregate functions available in SQL. The exact list is dependent upon the RDBMS. But most will 
have the basic functions: min(), max(), count(), avg() and sum().

**Try these queries:**

```sql
SELECT AVG(cost) FROM basic_cards;

SELECT MAX(cost) FROM basic_cards WHERE type = 'MINION'; 
```

Note on the last query we are select the most expensive card having a type of "MINION".

**Question1:** How would you find the cheapest card in basic_cards?

**Question2:** How would you cound the total number of cards? The number of cards of type 'HERO'?

### GROUP BY

The GROUP BY statement is often used with aggregate functions (COUNT, MAX, MIN, SUM, AVG) to group the result-set by one or more columns.

The syntax is:

```sql
SELECT column_name(s)
  FROM table_name
 WHERE condition
 GROUP BY column_name(s)
 ORDER BY column_name(s);
```

To select the most expensive card of each type, you would issue a query like:

```sql
SELECT type, MAX(cost) FROM basic_cards GROUP BY type;

-- To order the results by type, add an ORDER BY clause
SELECT type, MAX(cost) FROM basic_cards GROUP BY type ORDER BY type;

-- You can also order by the aggregate function
SELECT type, MAX(cost) FROM basic_cards GROUP BY type ORDER BY MAX(cost) DESC;
```

When grouping, every column in your SELECT clause that is NOT an aggregate function must be included in the GROUP BY clause.

For example, try this:

```sql
SELECT name, type, MAX(cost) FROM basic_cards GROUP BY type;
```
Although running the above query in SQLite is working fine but the result is not correct due to "name" is missing in the GROUP BY clause. In most relational database you should receive an error stating:

`ERROR:  column "basic_cards.name" must appear in the GROUP BY clause or be used in an aggregate function`

The correct SQL query should be 
```sql
SELECT name, type, MAX(cost) FROM basic_cards GROUP BY name, type;
```

### HAVING

The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.
You can think of the having clause as a where clause for aggregates.

The syntax is:

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);
```

This query will select the max cost for each player class if that max cost is greater than 5.

```sql
SELECT player_class, max(cost) FROM basic_cards GROUP BY player_class HAVING max(cost) > 5 ORDER BY max(cost) DESC;
```

**Practice questions:**

Table `workactivity`

|   Column    |  Type   | Modifiers |
| -------- | ----------- | ------- |
| worker_id   | integer | not null|
| computer_id | integer | |
| event_date  | date    | not null|
| tasks       | integer | |

`(worker_id, event_date)` is the PRIMARY KEY. 

This table shows the activities of workers and how many tasks they finished by the date. Each row is a record of a worker who logged in a computer and finished a number of tasks (it could be 0, no task was finished) on that date. Some sample records are listed below:

```
 worker_id | computer_id | event_date | tasks 
-----------+-------------+------------+-------
         1 |           2 | 2019-03-01 |     5
         1 |           2 | 2019-05-02 |     3
         2 |           3 | 2020-06-25 |     1
         3 |           1 | 2019-03-02 |     0
         3 |           4 | 2020-07-03 |     2
         1 |           3 | 2020-08-02 |     4
```

* Question 1: Write an SQL query that reports the first login date for each worker.
* Answer 1: `Your SQL query`

Expected result:
```
 worker_id | first_login 
-----------+-------------
         1 | 2019-03-01
         2 | 2020-06-25
         3 | 2019-03-02
```

* Question 2: Write a SQL query that reports the computer that is first logged in for each worker.
* Answer 2: `Your SQL query`

Expected result:
```
 worker_id | computer_id 
-----------+-------------
         1 |           2
         2 |           3
         3 |           1
```

* Question 3: Write an SQL query that reports for each worker and date, how many tasks finished so far by the worker. That is, the total number of tasks finished by the worker until that date.
* Answer 3: `Your SQL query`

Expected result:
```
 worker_id | event_date | tasks_finished_so_far 
-----------+------------+-----------------------
         1 | 2019-03-01 |                     5
         1 | 2019-05-02 |                     8
         1 | 2020-08-02 |                    12
         2 | 2020-06-25 |                     1
         3 | 2019-03-02 |                     0
         3 | 2020-07-03 |                     2
```

### Your turn
Now it's your turn to apply what you've learned. 
1. Submit all your answers (1-3) to Canvas `Lab 3 assignment`

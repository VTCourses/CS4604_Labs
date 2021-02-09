## Objective
Learn how to use a basic SELECT statement.

## Lab

### Setup

* Connect to a database
   ```
   > sqlite3 lab2.db
   ```

A data set for the collection of cards for [Hearthstone](https://playhearthstone.com/en-us/),
the popular online card game by Blizzard. This data set is freely available from 
[Kaggle](https://www.kaggle.com/jeradrose/hearthstone-cards).

### SQL SELECT

The SELECT statement is used to select data from a database.
Basic syntax is 

```sql
SELECT column1, column2, ...
  FROM table_name;
```

Here, column1, column2, ... are the field names of the table you want to select data from. 
If you want to select all the fields available in the table, use the following syntax:

```sql
SELECT * FROM table_name;
```

The following SQL statement selects the "type" and "name" columns from the "basic_cards" table.
**Try executing this query in your docker playground.**

```sql
SELECT type, name FROM basic_cards;
```

The following SQL statement selects all the columns from the "basic_cards" table.
**Try executing this query in your docker playground.**


```sql
SELECT * FROM basic_cards;
```

### SELECT DISTINCT

The SELECT DISTINCT statement is used to return only distinct (different) values.
Inside a table, a column often contains many duplicate values; and sometimes you only want to list the different (distinct) values.
The SELECT DISTINCT statement is used to return only distinct (different) values.

The syntax is:

```sql
SELECT DISTINCT column1, colun2, ...
  FROM table_name;
```

**Try this query:**

```sql
SELECT DISTINCT type FROM basic_cards;
```

### WHERE Clause

The WHERE clause is used to filter records.
The WHERE clause is used to extract only those records that fulfill a specified condition.

The syntax is:

```sql
SELECT column1, column2, ...
  FROM table_name
 WHERE boolean_condition;
```

**Try this query:**

```sql
SELECT name FROM basic_cards WHERE type = 'HERO';
```

We can also use other types of comparisons (note that numeric comparisons do not use quotes):

```sql
SELECT name, cost FROM basic_cards WHERE cost > 5;
```

All of the standard operators are valid: =, <> (or !=), >, <, >=, <=.
These are additional (and very useful operators):

| Operator | Description | Example |
| -------- | ----------- | ------- |
| BETWEEN | Between an inclusive range | WHERE x BETWEEN 3 AND 9 |
| LIKE | Search for a pattern | WHERE x LIKE 'Smi%' |
| IN | To specify multiple possible values for a column | WHERE x IN (3,5,7,9) |

We can also apply boolean operators AND, OR and NOT.

AND syntax is:

```sql
SELECT column1, column2, ...
  FROM table_name
 WHERE condition1 AND condition2 AND condition3 ...;
```

OR syntax is:

```sql
SELECT column1, column2, ...
  FROM table_name
 WHERE condition1 OR condition2 OR condition3 ...;
```

NOT syntax is:

```sql
SELECT column1, column2, ...
  FROM table_name
 WHERE NOT condition;
```

As you would expect, you can combine the various logical operators to form complex boolean expressions.

**Try these queries:**

```sql
SELECT name, cost FROM basic_cards WHERE cost < 5 AND player_class = 'DRUID';
```

```sql
SELECT name, cost FROM basic_cards WHERE cost < 5 OR player_class = 'DRUID';
```

```sql
SELECT name, player_class, cost FROM basic_cards WHERE cost < 5 OR player_class IN ('DRUID', 'HUNTER');
```

**Challenge question:**
<p>The <code>People</code> table holds all people including their parents. Every people has an Id, and there is also a column for the parent Id.</p>

| Id | Name  | Height | Parentid |
|----|-------|--------|-----------|
| 1  | Joe   | 193  | 3         |
| 2  | Henry | 168  | 4         |
| 3  | Sam   | 180  | NULL      |
| 4  | Max   | 178  | NULL      |


<p>Given the <code>People</code> table, write a SQL query that finds out People who is taller than their parents. For the above table, Joe is the only people who is taller than his parent.</p>


| Name |
|----------|
| Joe      |

Your answer 1: 

### Your turn

Now it's your turn to apply what you've learned. 
1. Submit your answer 1 to Canvas `Lab 2 assignment`
2. There is a larger table called **cards** in the database. Create two questions and submit them to Canvas `Lab 2 assignment`

For example:

**Question1:** How would you find the name, player class, and cost in the **cards** which that the cost is less than 5 or player class is either 'DRUID' or 'HUNTER'?

**Answer1:** 
```sql
SELECT name, player_class, cost FROM cards WHERE cost < 5 OR player_class IN ('DRUID', 'HUNTER');
```

Use what you find in the **cards** table and create these two questions. 

Question like **List all item in the cards table** would be too easy and boring. Surprise us with interesting questions! We are looking forward to seeing it!



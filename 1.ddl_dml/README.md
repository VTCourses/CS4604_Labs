## Objective
Learn to create database objects and manipulate data in the database.

## Lab

### Setup
* Prepare lab1 database
  ```
  > cp ../db/labs.db lab1.db
  ```

* Connect to a database
   ```
   > sqlite3 lab1.db
   ```

* Create a table: `basic_cards`
  ```
  sqlite> create table basic_cards as select card_id, player_class, type, name, cost from cards limit 30;
  ```

A data set for the collection of cards for [Hearthstone](https://playhearthstone.com/en-us/),
the popular online card game by Blizzard. This data set is freely available from 
[Kaggle](https://www.kaggle.com/jeradrose/hearthstone-cards).

### Data Definition Language (DDL)

#### Create table

The CREATE TABLE statement is used to create a new table in a database.

The syntax is:

```
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);
```

Every database supports the expected set of datatypes (though they don't always call them the same thing). To view the list of datatypes available in SQLite, click [here](https://www.sqlite.org/datatype3.html).

Try this query:

```
CREATE TABLE CS4604 (
  pid integer,
  name varchar(32),
  grade char(2)
);
```

See if a new `CS4604` table is created:
```
.table
```

See the structure of table `CS4604` (Table schema)
```
.schema CS4604
```

You can also add defaults and constraints at creation time:

```
-- Drop the table created in the previous step
DROP TABLE CS4604;
-- Re-create it with constraints
CREATE TABLE CS4604 (
  pid integer PRIMARY KEY,
  name varchar(32) NOT NULL,
  grade varchar(2) NOT NULL DEFAULT 'A',
  CHECK (grade IN ('A', 'A-', 'B+', 'B', 'B-', 'C+', 'C-', 'D+', 'D', 'D-', 'F'))
);
```

In the above example we are creating the CS4604 table with a primary key of pid. Every collumn requires a value (NOT NULL). The default value for grade is 'A' and whatever value assigned to grade must be a valid letter grade.

Note that constraints for primary key and not null are "inline" with the column definition, but that the check constraint is after column definitions. Other constraints (ie unique) can also be specified after the column definition.

Also note that it is not necessary to drop and recreate a table to add constraints. Constraints can be added using the `alter table` command shown below.

#### Alter table

The ALTER TABLE statement is used to add, delete, or modify columns in an existing table.

The ALTER TABLE statement is also used to add and drop various constraints on an existing table.

Important: SQLite supports a limited subset of ALTER TABLE. The ALTER TABLE command in SQLite allows the user to rename a table or add a new column to an existing table. It is not possible to rename a column, remove a column, or add or remove constraints from a table.

* Add a new column
  ```SQLite
  ALTER TABLE table_name
  ADD COLUMN column_definition;
  ```

  Try this query:
  ```
  ALTER TABLE CS4604
  ADD COLUMN location text;
  ```

  Note: SQL command in PostgreSQL
  ```
  ALTER TABLE table_name
  ADD column_name datatype;
  ```

* Rename a column (added supported in version 3.20.0)
  ```
  ALTER TABLE table_name
  RENAME COLUMN current_name TO new_name;
  ```

  Try this query:
  ```
  ALTER TABLE CS4604
  RENAME COLUMN location TO zipcode;
  ```

* Rename a table
  ```
  ALTER TABLE existing_table
  RENAME TO new_table;
  ```

  Try this query:
  ```
  ALTER TABLE CS4604 RENAME TO CSDatabase;
  ```

* Drop a column

  * PostgreSQL
  ```
  ALTER TABLE table_name
  DROP COLUMN column_name;
  ```

  Note: SQLite does not support ALTER TABLE DROP COLUMN statement. To drop a column, see more details [here](https://www.sqlitetutorial.net/sqlite-alter-table/)

* Add/Drop a constraint

  * PostgreSQL
  ```
  -- to add a constraint
  ALTER TABLE table_name
  ADD constraint_clause;

  -- to drop a constraint
  ALTER table table_name
  DROP constraint_clause;
  ```

### Data Manipulation Language (DML)

#### Inserts

##### INSERT INTO

The INSERT INTO statement is used to insert new records in a table.

The syntax is:

```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

Try this query:

```
INSERT INTO basic_cards (card_id, player_class, type, name, cost) VALUES ('CS4604', 'NINJA', 'HERO', 'Database Ninja', 20);
```

##### INSERT INTO SELECT

The INSERT INTO SELECT statement copies data from one table and inserts it into another table.

* INSERT INTO SELECT requires that data types in source and target tables match
* The existing records in the target table are unaffected

The syntax is:

```
INSERT INTO table2
SELECT * FROM table1
WHERE condition;
```

Alternately you can copy only some columns from one table into another table:

```
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```

Try this query:

```
INSERT INTO basic_cards (card_id, player_class, type, name, cost)
SELECT card_id, player_class, type, name, cost 
FROM cards
WHERE cost > 10;
```

Notice that this query is selecting cards from the `cards` table and inserting those whose `cost > 10` into the basic_cards table.

The `COUNT()` function returns the number of rows that matches a specified criterion.

The syntax is:
```
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

Try this query:

```
select count(*) from basic_cards;
```

* How many rows were inserted? `Answer 1:`
* Do you recall how to query for those rows? `Answer 2:`

#### Updates

The UPDATE statement is used to modify the existing records in a table.

The syntax is:

```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

**Note**: Be careful when updating records in a table! Notice the WHERE clause in the UPDATE statement. The WHERE clause specifies which record(s) should be updated. If you omit the WHERE clause, all records in the table will be updated!

Try this query:

```
UPDATE basic_cards SET cost = cost + 5 WHERE player_class = 'MAGE';
```

* How many rows were updated? `Answer 3:`

#### Deletes

The DELETE statement is used to delete existing records in a table.

The syntax is:

```
DELETE FROM table_name
WHERE condition;
```

**Note**: Be careful when deleting records in a table! Notice the WHERE clause in the DELETE statement. The WHERE clause specifies which record(s) that should be deleted. If you omit the WHERE clause, all records in the table will be deleted!

Try this query:

```
DELETE FROM basic_cards WHERE type = 'MINION';
```

* How many rows were deleted? `Answer 4:`

### Your turn

Now it's your turn to apply what you've learned. 
1. Submit all your answers (1-4) to Canvas `Lab 1 assignment`
2. There is a larger table called **cards** in the database. Do whatever you want on either `basic_cards` or `cards`. If you mess it up, just follow [Setup](#setup) and recreate the tables. 




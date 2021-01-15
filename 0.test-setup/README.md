## Objective
To get start using SQLite and PostgreSQL 

## Lab
### SQLite
* Connect to a SQLite database
   ```
   > sqlite3 lab0.db
   ```

   You should see a bunch of output that ends in something like this:
   ```
   SQLite version 3.7.17 2013-05-20 00:56:22
   Enter ".help" for instructions
   Enter SQL statements terminated with a ";"
   sqlite>
   ```

* Run a  SQL query
   ```
   sqlite> select 'it works!' as test;
   ```
   You are looking specifically for the `it works!` message.

* Exit out of SQLite
   ```
   sqlite> .quit
   ```

* See [Reference](../readme.md#reference) for more about SQLite usage

### PostgreSQL
* Connect to a SQLite database through a Jupter Notebook
   1. See [PostgreSQL on Jupter Notebooks](../readme.md#jupter-notebooks)
   2. Open [Lab_0.ipynb](Lab_0.ipynb) and follow the steps in the notebook

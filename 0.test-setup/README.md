## Objective
To get start using SQLite. 

## Lab
* Connect to a database
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

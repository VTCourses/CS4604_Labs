## Objective
Understanding indexing

## Lab 5

### Indexing

Indexes are used to retrieve data from the database very fast. The users cannot see the indexes, they are just used to speed up searches/queries.

#### Explain Analyze 

In order to see the impact of our indexing efforts, we will use the `EXPLAIN ANALYZE` feature of Postgresql.

Example:
```sql
postgres=# EXPLAIN ANALYZE SELECT count(*) FROM big_cards;
```


```python
query = """
EXPLAIN ANALYZE SELECT count(*) FROM big_cards;
"""

%sql $query
```

<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Aggregate  (cost=97949.57..97949.58 rows=1 width=0) (actual time=538.374..538.374 rows=1 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;-&gt;  Seq Scan on big_cards  (cost=0.00..90736.26 rows=2885326 width=0) (actual time=0.023..338.604 rows=2886656 loops=1)</td>
    </tr>
    <tr>
        <td>Total runtime: 538.414 ms</td>
    </tr>
</table>



**Note** that instead of returning the data, `EXPLAIN ANALYZE` provides a query plan detailing what approach the planner took to executing the statement provided along with the executiont time for the statement. These two pieces of information allow us to make performance tuning decisions about our query.

Most databases provide the ability to display "explain plans" or "query plans" used to execute queries. An explain plan is an ordered set of steps used by the RDBMS to access the requested data. 

Since SQL is declarative, there are typically a large number of alternative ways to execute a given query, with widely varying performance. When a query is submitted to the database, the query optimizer evaluates some of the different, correct possible plans for executing the query and returns what it considers the best option. Because query optimizers are imperfect, database users and administrators sometimes need to manually examine and tune the plans produced by the optimizer to get better performance. One way to to influence the plan is to create indexes. This creates additional possible (hopefully more performant) plans. However, because maintaining indexes can incur a performance penalty for updates, inserts and deletes we want to create the minimal set of indexes needed.

#### Using Indexes to improve performance

As a developer you know that your application will perform the query below *a lot*. It's not quite as fast as you would like it to be so you start performance tuning it.

**Query**: `select card_id, name from big_cards where race = 'TOTEM';`

You suspect an index will help, but before you make any changes you want to get a baseline explain plan and execution time. You do this using `EXPLAIN ANALYZE`.

**Execute**: `EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';`

```sql
postgres=# EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';
```


```python
query = """
EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';
"""

%sql $query
```

<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Seq Scan on big_cards  (cost=0.00..97949.57 rows=6732 width=23) (actual time=0.221..330.812 rows=8192 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;Filter: (race = &#x27;TOTEM&#x27;::text)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;Rows Removed by Filter: 2878464</td>
    </tr>
    <tr>
        <td>Total runtime: 331.246 ms</td>
    </tr>
</table>



You can see that the query take about 0.33seconds to execute 

You suspect that an index on the race column will help. Let's create it.

**Execute**: `CREATE INDEX IDX1_big_cards ON big_cards(race);`

**Execute**: `EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';`



```python
query = """
CREATE INDEX IDX1_big_cards ON big_cards(race);
"""

%sql $query
```

```python
query = """
EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';
"""

%sql $query
```

<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Bitmap Heap Scan on big_cards  (cost=128.77..19610.33 rows=6736 width=23) (actual time=2.160..28.843 rows=8192 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;Recheck Cond: (race = &#x27;TOTEM&#x27;::text)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;-&gt;  Bitmap Index Scan on idx1_big_cards  (cost=0.00..127.09 rows=6736 width=0) (actual time=1.101..1.101 rows=8192 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Index Cond: (race = &#x27;TOTEM&#x27;::text)</td>
    </tr>
    <tr>
        <td>Total runtime: 29.233 ms</td>
    </tr>
</table>



Congratulations! You have improved the query execution time by a lot! Looking at the explain plan, you see that the query fetches index rows (Bitmap Index Scan) and uses those rows to fetch the card_id and name values from big_cards (Bitmap Heap Scan). 

Would it be possible to satisfy the query with an index only and further speed up the query?

**Execute**: `CREATE INDEX IDX2_big_cards ON big_cards(race, card_id, name);`

**Execute**: `EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';`


```python
query = """
CREATE INDEX IDX2_big_cards ON big_cards(race, card_id, name);
"""

%sql $query
```

```python
query = """
EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';
"""

%sql $query
```


<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Bitmap Heap Scan on big_cards  (cost=128.77..19610.33 rows=6736 width=23) (actual time=2.110..29.715 rows=8192 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;Recheck Cond: (race = &#x27;TOTEM&#x27;::text)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;-&gt;  Bitmap Index Scan on idx1_big_cards  (cost=0.00..127.09 rows=6736 width=0) (actual time=1.067..1.067 rows=8192 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Index Cond: (race = &#x27;TOTEM&#x27;::text)</td>
    </tr>
    <tr>
        <td>Total runtime: 30.114 ms</td>
    </tr>
</table>



**Note**:
It's possible that your query, while perhaps faster, is still not being satisfied entirely by the index. The reason is that PostgreSQL maintains a [visibility map](https://www.postgresql.org/docs/9.5/static/storage-vm.html) of tuples for every relation. So even though all of the data that you need can be found in the index, the database still needs to check if each tuple is visible. You can read more about this [here](https://wiki.postgresql.org/wiki/Index-only_scans#What_types_of_queries_may_be_satisfied_by_an_index-only_scan.3F).

If you issue command `VACUUM big_cards;` and re-analyze you will likely see an explain plan that *is* satisfied by the index (and consequently much faster). However, subsequent updates to the table would cause this query to go back to the table to check the visibility map.


```python
query = """
VACUUM big_cards;
"""

%sql $query
```


```python
query = """
EXPLAIN ANALYZE SELECT card_id, name FROM big_cards WHERE race = 'TOTEM';
"""

%sql $query
```

<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Index Only Scan using idx2_big_cards on big_cards  (cost=0.00..294.88 rows=6736 width=23) (actual time=0.633..2.353 rows=8192 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;Index Cond: (race = &#x27;TOTEM&#x27;::text)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;Heap Fetches: 0</td>
    </tr>
    <tr>
        <td>Total runtime: 2.732 ms</td>
    </tr>
</table>



#### The performance cost of Indexes 

In general, we don't want to create unused indexes because they incur a performance penalty. The penalty is often minimal unless the application has a very high rate of updates. But it is something to be aware of.

Let's analyze an update of every row while our two indexes exist:

```sql
postgres=# explain analyze update big_cards set race = 'FOO';
```


```python
query = """
explain analyze update big_cards set race = 'FOO';
"""

%sql $query
```

<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Update on big_cards  (cost=0.00..90749.56 rows=2886656 width=314) (actual time=62942.649..62942.649 rows=0 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;-&gt;  Seq Scan on big_cards  (cost=0.00..90749.56 rows=2886656 width=314) (actual time=0.008..5041.202 rows=2886656 loops=1)</td>
    </tr>
    <tr>
        <td>Total runtime: 62942.678 ms</td>
    </tr>
</table>



Note the Execution time.

Now let's drop the indexes and try again:

```sql
postgres=# drop index idx1_big_cards;
DROP INDEX
postgres=# drop index idx2_big_cards;
DROP INDEX
postgres=# explain analyze update big_cards set race = 'BAR';
```


```python
query = """
drop index idx1_big_cards;
"""

%sql $query
```

```python
query = """
drop index idx2_big_cards;
"""

%sql $query
```


```python
query = """
explain analyze update big_cards set race = 'BAR';
"""

%sql $query
```

<table>
    <tr>
        <th>QUERY PLAN</th>
    </tr>
    <tr>
        <td>Update on big_cards  (cost=0.00..183033.05 rows=5822105 width=314) (actual time=27635.732..27635.732 rows=0 loops=1)</td>
    </tr>
    <tr>
        <td>&nbsp;&nbsp;-&gt;  Seq Scan on big_cards  (cost=0.00..183033.05 rows=5822105 width=314) (actual time=0.068..12564.359 rows=2886656 loops=1)</td>
    </tr>
    <tr>
        <td>Total runtime: 27635.758 ms</td>
    </tr>
</table>



Notice that the update took half as long without the indexes. 

The moral of this lab is: create the indexes you need, but *only* the indexes you need.


```python

```

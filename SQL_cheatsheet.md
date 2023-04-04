# Where clause

# Window Functions

## OVER:

[window_function] OVER (...)

notes: window_function can be any aggregate function such as -> COUNT(), SUM(), AVG(). Also

- RANK() => eg: RANK() OVER(ORDER BY editor_rating), output => 1,1,1,4

- DENSE_RANK()  => output => 1,1,1,2

- ROW_NUMBER() => output => 1,2,3,4

- NTILE(X) => distributes rows into x number of groups

- LEAD(col_name) => LEAD with a single argument in the parentheses looks at the next row in the given order and shows the value in the column specified as the argument.  eg: LEAD(name) OVER(ORDER BY opened). In general `LEAD(opened,2)`=> how many steps can also be specified.

- NTH_VALUE(x, n),LAG(),FIRST_VALUE(),LAST_VALUE().

RANK() with ORDER BY many columns => eg: `ROW_NUMBER() OVER(ORDER BY released DESC, updated DESC)`

### Rolling windows - ROWS() And RANGE()

- The query with RANGE sums the total_price for all rows which have their RANK() less than or equal to the rank of the current row.
- ROWS sums the total_price for all rows which have their ROW_NUMBER less than or equal to the row number of the current row.

`ROWS BETWEEN lower_bound AND upper_bound`

eg: `SUM(total_price) OVER(ORDER BY placed ROWS UNBOUNDED PRECEDING) AS running_total`, `total_price / AVG(total_price) OVER(ORDER BY placed ROWS BETWEEN 2 PRECEDING AND 2 FOLLOWING)
FROM single_order`

notes:

```UNBOUNDED PRECEDING – the first possible row.
PRECEDING – the n-th row before the current row (instead of n, write the number of your choice).
CURRENT ROW – simply current row.
FOLLOWING – the n-th row after the current row.
UNBOUNDED FOLLOWING – the last possible row.
```


With PARTITION BY, you can easily compute the statistics for the whole group but keep details about individual rows.

```
SELECT
  id,
  model,
  first_class_places,
  SUM(first_class_places) OVER (PARTITION BY model)
FROM train;
```

We can also combine => eg: `MAX(revenue) OVER(
PARTITION BY store_id
ORDER BY day 
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)`


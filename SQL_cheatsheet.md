# Where clause

# Window Functions

## OVER:

[window_function] OVER (...)

notes: window_function can be any aggregate function such as -> COUNT(), SUM(), AVG(). Also

RANK() => eg: RANK() OVER(ORDER BY editor_rating), output => 1,1,1,4

DENSE_RANK()  => output => 1,1,1,2

ROW_NUMBER() => output => 1,2,3,4

NTILE(X) => distributes rows into x number of groups

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


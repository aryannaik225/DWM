
# SQL Query Examples with Outputs

## Table Creation and Data Insertion

```sql
CREATE TABLE sales (
    region VARCHAR2(50),
    product VARCHAR2(50),
    year NUMBER,
    quarter NUMBER,
    amount NUMBER
);

INSERT INTO sales VALUES('North','Product A',2020,1,1000);
INSERT INTO sales VALUES('North','Product B',2020,1,1500);
INSERT INTO sales VALUES('North','Product A',2020,2,1200);
INSERT INTO sales VALUES('North','Product B',2020,2,1800);
INSERT INTO sales VALUES('South','Product A',2020,1,900);
INSERT INTO sales VALUES('South','Product B',2020,1,1300);
INSERT INTO sales VALUES('South','Product A',2020,2,1100);
INSERT INTO sales VALUES('South','Product B',2020,2,1700);

COMMIT;
```

## Output Examples

### Full Table

```sql
SELECT * FROM sales;
```

| Region | Product   | Year | Quarter | Amount |
|--------|-----------|------|---------|--------|
| North  | Product A | 2020 | 1       | 1000   |
| North  | Product B | 2020 | 1       | 1500   |
| North  | Product A | 2020 | 2       | 1200   |
| North  | Product B | 2020 | 2       | 1800   |
| South  | Product A | 2020 | 1       | 900    |
| South  | Product B | 2020 | 1       | 1300   |
| South  | Product A | 2020 | 2       | 1100   |
| South  | Product B | 2020 | 2       | 1700   |

### Slice Operation

```sql
SELECT * FROM sales WHERE region = 'North';
```

| Region | Product   | Year | Quarter | Amount |
|--------|-----------|------|---------|--------|
| North  | Product A | 2020 | 1       | 1000   |
| North  | Product B | 2020 | 1       | 1500   |
| North  | Product A | 2020 | 2       | 1200   |
| North  | Product B | 2020 | 2       | 1800   |

### Dice Operation

```sql
SELECT * FROM sales WHERE region = 'North' AND product = 'Product A';
```

| Region | Product   | Year | Quarter | Amount |
|--------|-----------|------|---------|--------|
| North  | Product A | 2020 | 1       | 1000   |
| North  | Product A | 2020 | 2       | 1200   |

### Drilldown (Roll Down)

```sql
SELECT region, product, year, SUM(amount) AS total_amount FROM sales GROUP BY ROLLUP(region, product, year);
```

| Region | Product   | Year | Total Amount |
|--------|-----------|------|--------------|
| North  | Product A | 2020 | 2200        |
| North  | Product A | NULL | 2200        |
| North  | Product B | 2020 | 3300        |
| North  | Product B | NULL | 3300        |
| North  | NULL      | NULL | 5500        |
| South  | Product A | 2020 | 2000        |
| South  | Product A | NULL | 2000        |
| South  | Product B | 2020 | 3000        |
| South  | Product B | NULL | 3000        |
| South  | NULL      | NULL | 5000        |
| NULL   | NULL      | NULL | 10500       |

### Rollup (Drill Up)

```sql
SELECT region, product, SUM(amount) AS total_amount FROM sales GROUP BY ROLLUP(region, product);
```

| Region | Product   | Total Amount |
|--------|-----------|--------------|
| North  | Product A | 2200         |
| North  | Product B | 3300         |
| North  | NULL      | 5500         |
| South  | Product A | 2000         |
| South  | Product B | 3000         |
| South  | NULL      | 5000         |
| NULL   | NULL      | 10500        |

### Pivot (Rotate)

```sql
SELECT * FROM (
    SELECT region, product, quarter, amount FROM sales
) 
PIVOT (
    SUM(amount)
    FOR quarter IN (1 AS Q1, 2 AS Q2)
);
```

| Region | Product   | Q1   | Q2   |
|--------|-----------|------|------|
| North  | Product A | 1000 | 1200 |
| North  | Product B | 1500 | 1800 |
| South  | Product A | 900  | 1100 |
| South  | Product B | 1300 | 1700 |

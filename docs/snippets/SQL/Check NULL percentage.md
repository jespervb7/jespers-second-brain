```SQL title="Check NULL percentage" linenums="1"
SELECT
    COUNT(*) AS total_rows,
    SUM(CASE WHEN column_name IS NULL THEN 1 ELSE 0 END) AS null_rows,
    100.0 *
    SUM(CASE WHEN column_name IS NULL THEN 1 ELSE 0 END)
    / COUNT(*) AS null_percentage
FROM table_name;
```
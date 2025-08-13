```SQL
SELECT
		sch.name							AS schema_name
	,	vw.schema_id						AS schema_id
	,	vw.name								AS view_name
	,	col.name							AS column_name
	,	col.column_id						AS column_order
	,	col.max_length						AS length_datatype
	,	col.precision						AS precision_datatype
	,	vw.create_date
	,	vw.modify_date
FROM sys.views vw
LEFT JOIN sys.columns col
ON col.object_id = vw.object_id
LEFT JOIN sys.schemas sch
ON sch.schema_id = vw.schema_id
ORDER BY vw.name, col.column_id
```

```SQL
SELECT
		sch.name							AS schema_name
	,	tbl.schema_id						AS schema_id
	,	tbl.name							AS table_name
	,	col.name							AS column_name
	,	col.column_id						AS column_order
	,	col.max_length						AS length_datatype
	,	col.precision						AS precision_datatype
	,	tbl.create_date
	,	tbl.modify_date
FROM sys.tables tbl
LEFT JOIN sys.columns col
ON col.object_id = tbl.object_id
LEFT JOIN sys.schemas sch
ON sch.schema_id = tbl.schema_id
WHERE sch.schema_id = 6
ORDER BY tbl.name, col.column_id
```

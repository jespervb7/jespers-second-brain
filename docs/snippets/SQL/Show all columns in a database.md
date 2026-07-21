```SQL title="Show all views with columns" linenums="1"
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

```SQL title="Show all tables with columns" linenums="1"
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
ORDER BY tbl.name, col.column_id
```

```SQL
SELECT
    class_desc
  , CASE WHEN class = 0 THEN DB_NAME()
         WHEN class = 1 THEN OBJECT_NAME(major_id)
         WHEN class = 3 THEN SCHEMA_NAME(major_id)
		 WHEN class = 32 THEN 'DATABASE_SCOPED_CREDENTIAL'
	END [Securable]
  , USER_NAME(grantee_principal_id) [User]
  , permission_name
  , state_desc
FROM sys.database_permissions
WHERE USER_NAME(grantee_principal_id) <> 'public'
ORDER BY 3, 2;

SELECT
		pr.name								AS principal_name
	,	pr.type_desc						AS principal_type
	,	o.name								AS object_name
	,	o.type_desc							AS object_type_description
	,	pe.permission_name
	,	pe.state_desc
	,	pr.principal_id
	,	o.create_date
	,	o.modify_date
FROM sys.database_permissions AS pe
JOIN sys.objects AS o
    ON pe.major_id = o.object_id
JOIN sys.database_principals AS pr
    ON pe.grantee_principal_id = pr.principal_id
WHERE o.type IN ('P')  -- stored procedure;
```

```SQL title="Show all grants for all users" linenums="1"
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
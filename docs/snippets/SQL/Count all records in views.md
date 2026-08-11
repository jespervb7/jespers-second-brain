```sql
-- Drop temp tables if they exist from a previous run
IF OBJECT_ID('tempdb..#ViewQueue')        IS NOT NULL DROP TABLE #ViewQueue;
IF OBJECT_ID('tempdb..#ViewRecordCounts') IS NOT NULL DROP TABLE #ViewRecordCounts;

-- Create a queue table using ROW_NUMBER() instead of IDENTITY
CREATE TABLE #ViewQueue (
    RowNum      INT,
    SchemaName  NVARCHAR(128),
    ViewName    NVARCHAR(128)
);

-- Create results temp table
CREATE TABLE #ViewRecordCounts (
    SchemaName   NVARCHAR(128),
    ViewName     NVARCHAR(128),
    RecordCount  BIGINT,
    Status       NVARCHAR(50),
    ErrorMessage NVARCHAR(4000)
);

-- Populate queue with ROW_NUMBER() to simulate IDENTITY
INSERT INTO #ViewQueue (RowNum, SchemaName, ViewName)
SELECT 
    ROW_NUMBER() OVER (ORDER BY SCHEMA_NAME(schema_id), name),
    SCHEMA_NAME(schema_id),
    name
FROM sys.views
WHERE is_ms_shipped = 0;

-- Loop variables
DECLARE @i          INT = 1;
DECLARE @maxRow     INT = (SELECT COUNT(*) FROM #ViewQueue);
DECLARE @SchemaName NVARCHAR(128);
DECLARE @ViewName   NVARCHAR(128);
DECLARE @sql        NVARCHAR(MAX);
DECLARE @count      BIGINT;

-- Iterate through each view by row number
WHILE @i <= @maxRow
BEGIN
    -- Pull the current view from the queue
    SELECT 
        @SchemaName = SchemaName,
        @ViewName   = ViewName
    FROM #ViewQueue
    WHERE RowNum = @i;

    BEGIN TRY
        SET @sql = N'SELECT @cnt = COUNT(*) FROM [' + @SchemaName + '].[' + @ViewName + ']';

        EXEC sp_executesql
            @sql,
            N'@cnt BIGINT OUTPUT',
            @cnt = @count OUTPUT;

        INSERT INTO #ViewRecordCounts (SchemaName, ViewName, RecordCount, Status, ErrorMessage)
        VALUES (@SchemaName, @ViewName, @count, 'SUCCESS', NULL);

    END TRY
    BEGIN CATCH
        INSERT INTO #ViewRecordCounts (SchemaName, ViewName, RecordCount, Status, ErrorMessage)
        VALUES (
            @SchemaName,
            @ViewName,
            NULL,
            'FAILED',
            'Error ' + CAST(ERROR_NUMBER() AS NVARCHAR(10)) + ': ' + ERROR_MESSAGE()
        );
    END CATCH;

    SET @i = @i + 1;
END;

-- Results with failures first
SELECT
    SchemaName,
    ViewName,
    RecordCount,
    Status,
    ErrorMessage
FROM #ViewRecordCounts
ORDER BY
    CASE WHEN Status = 'FAILED' THEN 0 ELSE 1 END,
    SchemaName,
    ViewName;

-- Summary
SELECT
    COUNT(*)                                     AS TotalViews,
    SUM(CASE WHEN Status = 'SUCCESS' THEN 1 END) AS Successful,
    SUM(CASE WHEN Status = 'FAILED'  THEN 1 END) AS Failed,
    SUM(RecordCount)                             AS TotalRecordsAcrossAllViews
FROM #ViewRecordCounts;
```

DECLARE @tableName NVARCHAR(256);
DECLARE @tableSchema NVARCHAR(256);
DECLARE @sql NVARCHAR(MAX) = '';

-- Create the cursor for selecting all table names and schemas from the information schema
DECLARE table_cursor CURSOR FOR 
SELECT TABLE_NAME, TABLE_SCHEMA 
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_TYPE = 'BASE TABLE';

-- Open the cursor
OPEN table_cursor;

-- Fetch the first row from the cursor
FETCH NEXT FROM table_cursor INTO @tableName, @tableSchema;

-- Loop through all tables
WHILE @@FETCH_STATUS = 0
BEGIN
    -- Construct and execute the SQL statement to drop each table
    SET @sql = 'DROP TABLE ' + QUOTENAME(@tableSchema) + '.' + QUOTENAME(@tableName);
    EXEC sp_executesql @sql;

    -- Fetch the next row from the cursor
    FETCH NEXT FROM table_cursor INTO @tableName, @tableSchema;
END;

-- Close and deallocate the cursor
CLOSE table_cursor;
DEALLOCATE table_cursor;

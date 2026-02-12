SELECT  
    @@SERVERNAME AS ServerName,
    DB_NAME() AS Dbname,
    p.name AS UserName,
    p.type_desc AS TypeOfLogin,
    pp.name AS DatabaseRoleAssigned,
    pp.type_desc AS TypeOfRole,
    obj.name AS TableName,
    perm.permission_name AS TablePermission,
    perm.state_desc AS PermissionState
FROM sys.database_principals p
LEFT JOIN sys.database_role_members roles 
    ON roles.member_principal_id = p.principal_id
LEFT JOIN sys.database_principals pp 
    ON roles.role_principal_id = pp.principal_id
LEFT JOIN sys.database_permissions perm 
    ON perm.grantee_principal_id = p.principal_id
LEFT JOIN sys.objects obj 
    ON perm.major_id = obj.object_id
WHERE p.type_desc NOT IN ('DATABASE_ROLE','ASYMMETRIC_KEY_MAPPED_USER')
AND p.name NOT IN ('sys','INFORMATION_SCHEMA','guest','dbo')
AND obj.name = 'CashTransaction_History'   -- 👈 your table name here
ORDER BY p.name;


SELECT 
    USER_NAME(perm.grantee_principal_id) AS UserName,
    perm.permission_name,
    perm.state_desc
FROM sys.database_permissions perm
JOIN sys.objects obj 
    ON perm.major_id = obj.object_id
WHERE obj.name = 'CashTransaction_History'
ORDER BY UserName;

SELECT  
    @@SERVERNAME AS ServerName,
    DB_NAME() AS Dbname,
    p.name AS UserName,
    p.type_desc AS TypeOfLogin,
    r.name AS DatabaseRoleAssigned,
    obj.name AS TableName,
    perm.permission_name AS TablePermission,
    perm.state_desc AS PermissionState
FROM sys.database_principals p

LEFT JOIN sys.database_role_members drm
    ON drm.member_principal_id = p.principal_id

LEFT JOIN sys.database_principals r
    ON drm.role_principal_id = r.principal_id

LEFT JOIN sys.database_permissions perm
    ON perm.grantee_principal_id = p.principal_id
    AND perm.class = 1   -- OBJECT permission only

LEFT JOIN sys.objects obj
    ON perm.major_id = obj.object_id
    AND obj.name = 'CashTransaction_History'   -- filter here instead

WHERE p.type_desc IN ('SQL_USER','WINDOWS_USER','WINDOWS_GROUP')
AND p.name NOT IN ('sys','INFORMATION_SCHEMA','guest','dbo')

ORDER BY p.name;





SELECT 
    dp.name AS UserName,
    dp.type_desc,
    HAS_PERMS_BY_NAME('dbo.CashTransaction_History', 'OBJECT', 'SELECT') AS CanSelect,
    HAS_PERMS_BY_NAME('dbo.CashTransaction_History', 'OBJECT', 'INSERT') AS CanInsert,
    HAS_PERMS_BY_NAME('dbo.CashTransaction_History', 'OBJECT', 'UPDATE') AS CanUpdate,
    HAS_PERMS_BY_NAME('dbo.CashTransaction_History', 'OBJECT', 'DELETE') AS CanDelete
FROM sys.database_principals dp
WHERE dp.type IN ('S','U','G')
AND dp.name NOT IN ('sys','INFORMATION_SCHEMA','guest','dbo');



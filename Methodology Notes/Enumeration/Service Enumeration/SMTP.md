## Nmap Footprinting of SMTP

```bash
nmap 10.129.14.128 -sC -sV -p25

## Check for open relay possibility
nmap 10.129.14.128 -p25 --script smtp-open-relay -v

## User enumeration script
nmap 10.129.14.128 -p25 --script smtp-enum-users -v
```


## Telnet verification of users

The command `VRFY` can be used to enumerate existing users on the system. However, this does not always work. 
```bash
telnet 10.129.14.128 25

VRFY root
```
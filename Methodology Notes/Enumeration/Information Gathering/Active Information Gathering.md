### Default Microsoft IIS Version Mapping

| IIS Version           | Operating System          |
|-----------------------|---------------------------|
| IIS 6.0               | Windows Server 2003       |
| IIS 7.0-8.5           | Windows Server 2008 / Windows Server 2008R2 |
| IIS 10.0 (v1607-v1709)| Windows Server 2016       |
| IIS 10.0 (v1809-)     | Windows Server 2019       |

### Curling HTTP Headers
```bash
export TARGET=www.google.com
curl -I "http://${TARGET}"
```

### Using 'WhatWeb'

```bash
whatweb -a3 https://www.facebook.com -v
```

### Wappalyzer Browser Extension

- https://www.wappalyzer.com/

### WafW00f WAF Fingerprinting

```bash
sudo apt install wafw00f -y
wafw00f -v https://www.tesla.com
```

### Active Subdomain Enumeration

- Refer to Service Enumeration Notes on DNS Enumeration

### Virtual Host Fuzzing

```bash
## Sample Command
ffuf -w /opt/useful/SecLists/Discovery/DNS/namelist.txt -u http://192.168.10.10 -H "HOST: FUZZ.randomtarget.com" -fs 612

## Match specific content found i.e. HTB
ffuf -w /opt/useful/SecLists/Discovery/DNS/namelist.txt -u http://192.168.10.10 -H "HOST: FUZZ.randomtarget.com" -fs 612 -mr "HTB"
```

### web Crawling

```bash
ffuf -recursion -recursion-depth 1 -u http://192.168.10.10/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/raft-small-directories-lowercase.txt
```

### Directory Brute Forcing

```bash
## Using gobuster
gobuster dir -u http://umbrella.htb -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt,json,xml,sh,cgi,yaml
```

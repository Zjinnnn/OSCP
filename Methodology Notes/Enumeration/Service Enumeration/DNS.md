### Dig
```bash
dig any www.inlanefreight.com
```

### Subdomain brute-force with Bash
```bash
for sub in $(cat /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

### Subdomain brute-force with DNSEnum
```bash
dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomain.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt --threads 90 inlanefreight.htb
```

### Gobuster Enumeration
```bash
gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt
```

### Enumeration for vHosts

```bash
## Using gobuster
gobuster vhost -u inlanefreight.local -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt

## Using ffuf
ffuf -w usr/share/seclists/SecLists-master/Discovery/DNS/subdomains-top1million-110000.txt:FUZZ -u http://10.129.203.101/ -H 'Host: FUZZ.inlanefreight.local' -fs 612

## Using wfuzz
wfuzz -u http://10.10.11.177 -H "Host: FUZZ.siteisup.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --hh 1131
```

### Zone Enumeration

```bash
## First zone enumeration
dig axfr inlanefreight.htb @10.129.127.213 > dns.txt

## Extract and clean up domains in file
awk '{ print $1 }' dns.txt 

## Subdomain zone enumeration
cat dns.txt | while read i; do dig axfr $i @10.129.127.213; done > enumerated.txt
```
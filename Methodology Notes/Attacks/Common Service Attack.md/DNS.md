## Enumeration

Refer to Service enumeration.

## DNS Zone Transfer

```bash
dig AXFR @ns1.inlanefreight.htb inlanefreight.htb

## fierce tool to enumerate all DNS servers of root domain and scan for zone transfer
fierce --domain zonetransfer.me
```

## Domain Takeovers & Subdomain Enumeration

```bash
## Subdomain Enumeration
./subfinder -d inlanefreight.com -v  

## Subbrute - DNS bruteforcing
git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1
cd subbrute
echo "ns1.inlanefreight.com" > ./resolvers.txt
./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txt
```

### Excellent reference for a subdomain takeover vulnerability
https://github.com/EdOverflow/can-i-take-over-xyz

## DNS Spoofing

### Ettercap 

```bash
## edit config file to map to target domain
cat /etc/ettercap/etter.dns

## Run from GUI
```

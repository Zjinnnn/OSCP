### Using 'WHOIS'

```bash
whois domain.com 
```

### Using 'nslookup'

```bash
## Optional inclusion of nameserver
nslookup facebook.com nameserver

## Querying for specific type of record
nslookup -type=TXT facebook.com nameserver

## Querying for any existing records 
nslookup -query=ANY facebook.com nameserver
```

### Using 'Dig'

```bash
dig facebook.com @8.8.8.8

## Dig for any existing records
dig any google.com @8.8.8.8

## Test for zone transfer
dig axfr google.com @8.8.8.8
```

### Using `host`
```bash
host www.megacorpone.com

## Searching for a particular type of DNS record
host -t mx megacorpone.com
```

### Using 'dnsrecon'

```bash
dnsrecon -d megacorpone.com -t std

## Using a specific wordlist to bruteforce
dnsrecon -d megacorpone.com -D ~/list.txt -t brt
```

### Using TheHarvester

```bash
## Create sources.txt file
echo -e "baidu\nbufferoverun\ncrtsh\nhackertarget\notx\nprojectdiscovery\nrapiddns\nsublist3r\nthreatcrowd\ntrello\nurlscan\nvhost\nvirustotal\nzoomeye" > sources.txt

## Piping of sources to automate TheHarvester 
export TARGET="facebook.com"
cat sources.txt | while read source; do theHarvester -d "${TARGET}" -b $source -f "${source}_${TARGET}";done

## Extract all subdomains
cat *.json | jq -r '.hosts[]' 2>/dev/null | cut -d':' -f 1 | sort -u > "${TARGET}_theHarvester.txt"
```

---

### Searching websites by subdomains
- https://subdomainfinder.c99.nl/
- https://searchdns.netcraft.com
- https://www.virustotal.com/gui/home/search
- https://censys.io
- https://crt.sh

## Google Dorking
- https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06
- https://dorksearch.com/

## Netcraft
- https://searchdns.netcraft.com

## Github Dorking
- https://github.com/gitleaks/gitleaks
- https://github.com/techgaun/github-dorks
- https://gist.github.com/bonniss/4f0de4f599708c5268134225dda003e0

## Shodan 
- https://www.shodan.io/
- https://cheatography.com/sir-slammington/cheat-sheets/shodan

## Analyse Headers & SSL
- https://www.ssllabs.com/ssltest/
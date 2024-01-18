### Nmap Port Scanning

Nmap scanning techniques
- Host discovery
- Port scanning
- Service enumeration and detection
- OS detection
- Scriptable interaction with target service

```bash
## Fast & Aggressive TCP Scan
nmap -v -p- -O -sS -T5 $ip

## Initial TCP Scan
nmap -sC -sV -O -oA nmap/initial $ip

## Full TCP Scan
nmap -sC -sV -O -p- -oA nmap/full $ip 

## Full UDP Scan
nmap -sU -O -p- -oA nmap/udp $ip

## Standard Scan (TCP Connect Scan & disables ICMP)
nmap -sT -Pn -p- --min-rate 5000 $ip 

## Locating NSE Scripts
/usr/share/nmap/scripts/
cat script.db | grep "\"vuln\""
```
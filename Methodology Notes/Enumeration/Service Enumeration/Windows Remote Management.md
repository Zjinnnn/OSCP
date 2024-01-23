### Footprinting RDP

```bash
nmap -sV -sC 10.129.201.248 -p 3389 --script rdp*
```

### RDP Security Check

```bash
## Installation
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check

## Running the script
./rdp-sec-check.pl 10.129.201.248
```

### Footprinting WinRM

WinRM - Windows Remote Managment
```bash
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

### Footprinting WMI

WMI - Windows Management Instrumentation
```bash
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```
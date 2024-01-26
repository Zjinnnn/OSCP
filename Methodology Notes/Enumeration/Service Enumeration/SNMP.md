### Common OID
| OID                       | Description             |
|---------------------------|-------------------------|
| 1.3.6.1.2.1.25.1.6.0     | System Processes        |
| 1.3.6.1.2.1.25.4.2.1.2   | Running Programs        |
| 1.3.6.1.2.1.25.4.2.1.4   | Processes Path          |
| 1.3.6.1.2.1.25.2.3.1.4   | Storage Units           |
| 1.3.6.1.2.1.25.6.3.1.2   | Software Name           |
| 1.3.6.1.4.1.77.1.2.25    | User Accounts           |
| 1.3.6.1.2.1.6.13.1.3     | TCP Local Ports         |

### Onesixtyone

```bash
## Brute-force names of community strings
onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt 10.129.14.128
```

### SNMPwalk 

```bash
## Query OIDs with their information; -c refers to 'public' community string
snmpwalk -v2c -c public 10.129.14.128
```

### braa

```bash
## Brute-force individual OIDs and enumerate information behind them
braa <community string>@<IP>:.1.3.6.*    # Syntax
braa public@10.129.14.128:.1.3.6.*
```
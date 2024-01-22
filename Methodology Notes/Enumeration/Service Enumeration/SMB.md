### RPC Client for SMB Enumeration

```bash
## Connect to SMB server
rpcclient -U "" 10.129.14.128

## Enumerate Server information
srvinfo

## User Enumeration
enumdomusers

## Brute-forcing RIDs
for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```

### Samrdump Python

```bash
samrdump.py 10.129.14.128
```

### SMBMap

```bash
smbmap -H 10.129.14.128
```

### CrackMapExec

```bash
crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```

### Enum4Linux

```bash
./enum4linux-ng.py 10.129.14.128 -A
```
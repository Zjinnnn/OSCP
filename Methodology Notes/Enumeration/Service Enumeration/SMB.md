### SMB Syntax for RPC Client

|Query|Description|
|---|---|
|srvinfo|Server information.|
|enumdomains|Enumerate all domains that are deployed in the network.|
|querydominfo|Provides domain, server, and user information of deployed domains.|
|netshareenumall|Enumerates all available shares.|
|netsharegetinfo|Provides information about a specific share.|
|enumdomusers|Enumerates all domain users.|
|queryuser <RID>|Provides information about a specific user.|

### RPC Client for SMB Enumeration

```bash
## Connect to SMB server
rpcclient -U "" 10.129.14.128

## Brute-forcing RIDs
for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```

### Impacket Samrdump Python

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

### Windows Host - Powershell
```powershell
## Listing shares
net view \\dc01 /all
```

### SMBClient

```bash
## List shares with a null session
smbclient -N -L //10.129.14.128

## Connecting to a share
smbclient //10.10.10.100/sharename
```
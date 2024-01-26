### Nmap Footprinting of SMTP

```bash
nmap 10.129.14.128 -sC -sV -p25

## Check for open relay possibility
nmap 10.129.14.128 -p25 --script smtp-open-relay -v

## User enumeration script
nmap 10.129.14.128 -p25 --script smtp-enum-users -v
```

### SMTP User Enumeration

```bash
smtp-user-enum -M VRFY -U /usr/share/wordlists/wfuzz/general/medium.txt -t 10.129.192.74 -w 15 -v
```

### Telnet verification of users

The command `VRFY` can be used to enumerate existing users on the system. However, this does not always work. 
```bash
telnet 10.129.14.128 25

VRFY root
```

### Windows Host - Powershell

```powershell
Test-NetConnection -Port 25 192.168.50.8
```

### SMTP Syntax

| Command    | Description                                                                 |
|------------|-----------------------------------------------------------------------------|
| AUTH PLAIN | AUTH is a service extension used to authenticate the client.                |
| HELO       | The client logs in with its computer name and thus starts the session.      |
| MAIL FROM  | The client names the email sender.                                          |
| RCPT TO    | The client names the email recipient.                                       |
| DATA       | The client initiates the transmission of the email.                         |
| RSET       | The client aborts the initiated transmission but keeps the connection.     |
| VRFY       | The client checks if a mailbox is available for message transfer (Test if a user exists)           |
| EXPN       | The client also checks if a mailbox is available for messaging with this command. |
| NOOP       | The client requests a response from the server to prevent disconnection due to time-out. |
| QUIT       | The client terminates the session.                                          |
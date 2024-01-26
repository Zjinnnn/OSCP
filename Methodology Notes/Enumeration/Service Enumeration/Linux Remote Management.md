### SSH - Auditing

`ssh-audit.py` is a tool that can be used to fingerprint the SSH server.
```bash
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit

./ssh-audit.py 10.129.14.132
```

### Rsynce Abuse

Rsync can be abused.
```bash
## Scanning for Rsync
sudo nmap -sV -p 873 127.0.0.1

## Probing for accessible shares
nc -nv 127.0.0.1 873

## Enumerating open share
rsync -av --list-only rsync://127.0.0.1/dev
```

### Deprecated R-Services Abuse

```bash
## Scanning for R-Services
sudo nmap -sV -p 512,513,514 10.0.17.2
```
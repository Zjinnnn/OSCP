## Protected Files

### Hunting for files

```bash
for ext in $(echo ".xls .xls* .xltx .csv .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

### Hunting for SSH keys

```bash
grep -rnw "PRIVATE KEY" /* 2>/dev/null | grep ":1"

## Encrypted SSH keys
cat /home/cry0l1t3/.ssh/SSH.private

## Convert to SSH hash
ssh2john.py SSH.private > ssh.hash

## Crack using John
john --wordlist=rockyou.txt ssh.hash
```

## Cracking Documents

### Cracking Microsoft Office documents

```bash
office2john.py Protected.docx > protected-docx.hash

john --wordlist=rockyou.txt protected-docx.has
```

### Cracking PDFs

```bash
pdf2john.py PDF.pdf > pdf.hash

john --wordlist=rockyou.txt pdf.hash
```

### Cracking ZIP

```bash
zip2john ZIP.zip > zip.hash

john --wordlist=rockyou.txt zip.hash
```

### Cracking OpenSSL Encrypted Archives

```bash
## Check file type 
file GZIP.zip

## Use for-loop to display extracted contents
for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done
```

### Cracking BitLocker Encrypted Drives

```bash
bitlocker2john -i Backup.vhd > backup.hashes

grep "bitlocker\$0" backup.hashes > backup.hash

## Use hashcat to crack
hashcat -m 22100 backup.hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -o backup.cracked
```


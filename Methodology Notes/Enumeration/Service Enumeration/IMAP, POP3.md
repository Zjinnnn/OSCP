### Footprinting IMAP/POP3

```bash
nmap 10.129.14.128 -sV -p110,142,993,995 -sC
```

### cURL Interaction

```bash
## Can observe how connection is being made
curl -k 'imaps://10.129.14.128' --user user:p4ssword -v
```

### OpenSSL Interaction

```bash
## TLS encrypted interaction POP3
openssl s_client -connect 10.129.14.128:pop3s

## TLS encrypted interaction IMAP
openssl s_client -connect 10.129.14.128:imaps
```

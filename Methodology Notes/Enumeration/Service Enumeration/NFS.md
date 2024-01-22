### Nmap NFS Footprinting

```bash
nmap 10.129.14.128 -p111,2049 -sV -sC

nmap --script nfs* 10.129.14.128 -sV -p111,2049
```

### Mounting NFS Share

```bash
mkdir target-NFS

sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock

cd target-NFS
```

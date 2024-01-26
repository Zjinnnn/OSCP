### Nmap-SID Brute-forcing

```bash
sudo nmap -p1521 -sV 10.129.204.235 --open

sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

### ODAT Enumeration

Setup Script for ODAT
``` bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien python3-pip -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
sudo apt install oracle-instantclient-basic oracle-instantclient-devel oracle-instantclient-sqlplus -y
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor pycryptodome passlib python-libnmap
sudo pip3 install argcomplete && sudo activate-global-python-argcomplete
```

```bash
./odat.py all -s 10.129.204.235
```
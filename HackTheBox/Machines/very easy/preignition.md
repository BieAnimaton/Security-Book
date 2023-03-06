ping 10.129.233.137

echo "10.129.233.137 preignition.htb" >> /etc/hosts

nmap -h
nmap preignition.htb
nmap -sCV preignition.htb

gobuster
gobuster dir -h
gobuster dir -x .php -u preignition.htb -w ../raft-medium-directories.txt -> get /admin.php

access /admin.php
log in with admin:admin

get flag
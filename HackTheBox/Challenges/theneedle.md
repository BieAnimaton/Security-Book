download file

Install binwalk
binwalk --extract firmware.bin --run-as=root

cd _firmware.bin.extracted
grep -rn "./" -e "login" -> find telnet
find ./ -name sign -> read files

telnet <ip> <port>
name and pass found

get flags
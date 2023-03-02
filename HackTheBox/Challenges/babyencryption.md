download files

nano decrypt.py
------------------------------------------------------------
import string
import binascii

def decryption(msg):
    ct = []
    for char in msg:
        ct.append((char - 18) * pow(123, -1, 256) % 256)
    return bytes(ct).decode('ISO-8859-1')

f = open("msg.enc", "r")
flag = decryption(binascii.unhexlify(f.read()))
print(flag)
------------------------------------------------------------

python3 decrypt.py

get flag
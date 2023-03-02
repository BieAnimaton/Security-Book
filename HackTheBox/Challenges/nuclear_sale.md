download file

Open extracted file with WireShark - filter for SMTP
Collect three encryptions

nano decrypt.py
---------------------------------------------------------------------------------------------------
from binascii import unhexlify

enc_flag = unhexlify('6b65813f4fe991efe2042f79988a3b2f2559d358e55f2fa373e53b1965b5bb2b175cf039')
ct = unhexlify('fd034c32294bfa6ab44a28892e75c4f24d8e71b41cfb9a81a634b90e6238443a813a3d34')
ct2 = unhexlify('de328f76159108f7653a5883decb8dec06b0fd9bc8d0dd7dade1f04836b8a07da20bfe70')
flag = ''
#Note the length of all the above texts are 36 bytes after decoding from ascii
for i,j in enumerate(ct):
	flag += chr(j ^ ct2[i] ^ enc_flag[i])
print(flag)
---------------------------------------------------------------------------------------------------

python3 decrypt.py

get flag
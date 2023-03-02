download files

open index.html on browser
login with admin:admin
get dashboard with HTB{<emoji>}

go to file /js
open login.js

the javascript is obfuscated
https://deobfuscate.io -> first deobfuscation
https://lelinhtinh.github.io/de4js/ -> second deobfuscation

look at end of the file
----------------------------------------------------------------------------------------------------------
...
var res = String.fromCharCode(72, 84, 66, 123, 87, 51, 76, 99, 48, 109, 51, 95, 55, 48, 95, 74, 52, 86, 52, 53, 67, 82, 49, 112, 55, 95, 100, 51, 48, 98, 70, 117, 53, 67, 52, 55, 49, 48, 78, 125, 10);
----------------------------------------------------------------------------------------------------------

open javascript online editor
----------------------------------------------------------------------------------------------------------
var res = String.fromCharCode(72, 84, 66, 123, 87, 51, 76, 99, 48, 109, 51, 95, 55, 48, 95, 74, 52, 86, 52, 53, 67, 82, 49, 112, 55, 95, 100, 51, 48, 98, 70, 117, 53, 67, 52, 55, 49, 48, 78, 125, 10);
console.log(res)
----------------------------------------------------------------------------------------------------------

get flag
---
title: "Emojimadness"
ctf: "ESIEACTF"
annee: "2018"
author: "zTeeed"
published: true
---

<br />
<a href="/writeup-scripts/2017-2018/ESIEACTF/emojimadness/file.js">download file.js</a>
<br />
<a href="/writeup-scripts/2017-2018/ESIEACTF/emojimadness/solve.py">download solve.py</a>
<br />
<br />
il s'agit d'un challenge d'obfucation, cependant, rien ne sert d'essayer de comprendre le code, on dΓ©code toutes les chaines de caractΓ¨res que l'on trouve dans le code et on obtient le flag
<br />
<br />
file.js
```js
eval("function π(π΄){alert(π΄)} function π(π΄){return window.prompt(π΄)}");eval("function π¬(π·, π){return π·^π;}");eval("π¨ = '='");eval("π = π¨.charCodeAt(0).toString()[0];");eval("πΆ = π¨.charCodeAt(0).toString()[1];");eval("function π²(π·, π){return π^π·;}");eval("function π(π·){return btoa(π·);} function π(π·){return atob(π·);}");eval("function π(π·){var arr = [];for (var π³ = (parseInt(πΆ)-parseInt(πΆ)), π° = π·.length; π³ < π°; π³ ++) {var π­ = Number(π·.charCodeAt(π³)).toString(parseInt(πΆ+π));arr.push(π­);}return arr.join('');}");eval("function π(π·){var π­ = π·.toString();var str = '';for (var π = (parseInt(πΆ)-parseInt(πΆ)); π < π­.length; π += (parseInt(πΆ)+parseInt(πΆ)))str += String.fromCharCode(parseInt(π­.substr(π, (parseInt(πΆ)+parseInt(πΆ))), parseInt(πΆ+π)));return str;}");eval("function π(){π = π(π('456e7472657a206c65206d6f74206465207061737365202f20456e7465722070617373776f7264')); if(π(π(π))=='52564e46653070545831526f4e48526652476c7964486c664a6c39544d33683558307730626d64314e47637a66513d3d'){π(π('R09PRCBKT0I='));}else{π(π('VE9PIEJBRA=='));}}");eval("function π(){π = π(π('456e7472657a206c65206d6f74206465207061737365202f20456e7465722070617373776f7264')); if(π(π(π))=='52564e46653164796232356e58305a7359576439'){π(π('R09PRCBKT0I='));}else{π(π('VE9PIEJBRA=='));}}");eval("π()");eval("function π(){π = π(π('456e7472657a206c65206d6f74206465207061737365202f20456e7465722070617373776f7264')); if(π(π(π))=='52564e4665315279655639425a324670626934754c6935454f6e303d'){π(π('R09PRCBKT0I='));}else{π(π('VE9PIEJBRA=='));}}");eval("function πͺ(π·){var arr = [];for (var π³ = (parseInt(πΆ)-parseInt(πΆ)), π° = π·.length; π³ < π°; π³ ++) {var π­ = Number(π·.charCodeAt(π³)).toString(parseInt(πΆ+π));arr.push(π­);}return arr.join('');}");
```
solve.py
```python
#! /usr/bin/env python3
import binascii
import base64

a="456e7472657a206c65206d6f74206465207061737365202f20456e7465722070617373776f7264"
b=binascii.unhexlify(a)
print(b)
c="52564e46653070545831526f4e48526652476c7964486c664a6c39544d33683558307730626d64314e47637a66513d3d"
d=binascii.unhexlify(c)
print(d)
#d="RVNFe0pTX1RoNHRfRGlydHlfJl9TM3h5X0w0bmd1NGczfQ=="
e=base64.b64decode(d)
print(e)
```

---
title: "CRYPTO.Hvítur\_Kassi\_(1)"
subtitle: "Challenge de cryptographie du CTF préqualifications de l'ECSC"
published: true
author: "Headorteil"
ctf: "ECSC préqualifications"
annee: "2019"
---

# L'énoncé

Hvítur Kassi (1)

Saurez-vous trouver l'entrée m qui affiche le flag ?

Le flag est de la forme ECSC{m}.

# TL;DR

On nous donne le code d'un algorithme qui chiffre son input et qui vérifie que l'output est bien "ECSC2019". On nous précise également "Usage: {} \<flag\>", le but est donc de reverse le programme (pour info, c'est une implementation de SKINNY-64-128).

# Analyse

## Le code

```python
#!/usr/bin/python -u

import sys
import zlib
import base64
import pickle
import string

def check_flag(flag):
	WB = """
        #Je ne recopie pas le WB ici, il est trop long
        """

	if len(flag) != 16 or not set(flag).issubset(string.hexdigits):
		sys.stdout.write("Error: the plaintext must contain 16 hexadecimal digits ([0-9a-f])\n")
		exit(1)

	s = [int(_, 16) for _ in flag]

	WB = pickle.loads(zlib.decompress(base64.decodestring(WB)))

	for r in range(36):
	    s = [ WB[256*r+16*pos+s[pos]] for pos in range(16) ]

	    tmp = [
	        WB[11520+3072*r+16*s[0]+s[10]],
	        WB[12288+3072*r+16*s[1]+s[11]],
	        WB[13056+3072*r+16*s[2]+s[ 8]],
	        WB[13824+3072*r+16*s[3]+s[ 9]],
	    ]

	    s = [
	        WB[11776+3072*r+16*tmp[0]+s[13]],
	        WB[12544+3072*r+16*tmp[1]+s[14]],
	        WB[13312+3072*r+16*tmp[2]+s[15]],
	        WB[14080+3072*r+16*tmp[3]+s[12]],
	        WB[9216+64*r+s[0]],
	        WB[9232+64*r+s[1]],
	        WB[9248+64*r+s[2]],
	        WB[9264+64*r+s[3]],
	        WB[12032+3072*r+16*s[7]+s[10]],
	        WB[12800+3072*r+16*s[4]+s[11]],
	        WB[13568+3072*r+16*s[5]+s[ 8]],
	        WB[14336+3072*r+16*s[6]+s[ 9]],
	        tmp[0],
	        tmp[1],
	        tmp[2],
	        tmp[3],
	    ]

	res = 0L
	for x in s: res = 16*res+x
	res = "{:016x}".format(res)
	if res.decode("hex") == "ECSC2019":
                {% raw %}
		print "Nice! You can enter this flag: ECSC{%s}." % flag
                {% endraw %}

if __name__ == "__main__":
	if len(sys.argv) != 2:
		print "Usage: {} <flag>".format(sys.argv[0])
		exit(1)
```

Le but est donc de trouver l'argument tel que l'output est ECSC2019.

## Réflexion...

On cherche d'abord l'output recherché après la boucle for. C'est tout simplement le tableau des valeurs entières correspondant à la valeur en hexadecimal de ECSC2019.

Ensuite, on va essayer de reverse la deuxième partie de la dernière étape de la boucle puis la première et enfin réitérer le processus pour arriver au final au flag.

# Résolution

## Reverse les étapes boucle for une par une

```python
	    tmp = [
	        WB[11520+3072*r+16*s[0]+s[10]],
	        WB[12288+3072*r+16*s[1]+s[11]],
	        WB[13056+3072*r+16*s[2]+s[ 8]],
	        WB[13824+3072*r+16*s[3]+s[ 9]],
	    ]

	    s = [
	        WB[11776+3072*r+16*tmp[0]+s[13]],
	        WB[12544+3072*r+16*tmp[1]+s[14]],
	        WB[13312+3072*r+16*tmp[2]+s[15]],
	        WB[14080+3072*r+16*tmp[3]+s[12]],
	        WB[9216+64*r+s[0]],
	        WB[9232+64*r+s[1]],
	        WB[9248+64*r+s[2]],
	        WB[9264+64*r+s[3]],
	        WB[12032+3072*r+16*s[7]+s[10]],
	        WB[12800+3072*r+16*s[4]+s[11]],
	        WB[13568+3072*r+16*s[5]+s[ 8]],
	        WB[14336+3072*r+16*s[6]+s[ 9]],
	        tmp[0],
	        tmp[1],
	        tmp[2],
	        tmp[3],
	    ]
```

On remarque que pour obtenir s[i] au rang n pour i allant de 4 à 7, on n'a besoin que des s[i] allant de 0 à 3 au rang n-1, pour obtenir les s[i] au rang n pour i allant de 12 à 15, on n'a besoin que des s[i] allant de 0 à 3 qu'on a donc déjà et des s[i] allant de 8 à 10. C'est le même schéma pour les autres valeurs de s.

On peut construire le tableau d'association.

| s au rang n | s au rang n-1 |
|-------------|---------------|
| 0           | 0 / 10 / 13   |
| 1           | 1 / 11 / 14   |
| 2           | 2 / 8 / 15    |
| 3           | 3 / 9 / 12    |
| 4           | 0             |
| 5           | 1             |
| 6           | 2             |
| 7           | 3             |
| 8           | 7 / 10        |
| 9           | 4 / 11        |
| 10          | 5 / 8         |
| 11          | 6 / 9         |
| 12          | 0 / 10        |
| 13          | 1 / 11        |
| 14          | 2 / 8         |
| 15          | 3 / 9         |

On peut donc obtenir s au rang n-1 grâce à s au rang n en trouvant d'abord s[0] / s[1] / s[2] / s[3], puis en trouvant s[10] / s[11] / s[8] / s[9] grâce aux données trouvées au préalable.On peut ensuite trouver s[7] / s[4] / s[5] / s[6] et enfin s[13] / s[14] / s[15] / s[12].

Dans WB, on n'a que des valeurs entre 0 et 15, pour passer de s[i] au rang n à s[i] au rang n-1, il suffit de tester tous les i entre 0 et 15 WB[x + i] avec ```x = "11776+3072*r+16*tmp[0]"``` par exemple pour s[0].

On peut faire la même chose pour la 1ere partie de la boucle for.

## Implémentation

```python
#!/usr/bin/python2 -u


import zlib
import base64
import pickle
import binascii


def solve(goal):
	WB = """
        #Je ne recopie pas le WB ici, il est trop long
        """

        WB = pickle.loads(zlib.decompress(base64.decodestring(WB)))

        for r in range(35, -1, -1):

            #Ici on reverse d'abord la 2eme partie de la boucle

            before = [-1]*16

            for j in range(16):
                if WB[9216+64*r+j] == goal[4]:
                    before[0] = j
            for j in range(16):
                if WB[9232+64*r+j] == goal[5]:
                    before[1] = j
            for j in range(16):
                if WB[9248+64*r+j] == goal[6]:
                    before[2] = j
            for j in range(16):
                if WB[9264+64*r+j] == goal[7]:
                    before[3] = j

            for j in range(16):
                if WB[11520+3072*r+16*before[0]+j] == goal[12]:
                    before[10] = j
            for j in range(16):
                if WB[12288+3072*r+16*before[1]+j] == goal[13]:
                    before[11] = j
            for j in range(16):
                if WB[13056+3072*r+16*before[2]+j] == goal[14]:
                    before[8] = j
            for j in range(16):
                if WB[13824+3072*r+16*before[3]+j] == goal[15]:
                    before[9] = j

            for j in range(16):
                if WB[12032+3072*r+16*j+before[10]] == goal[8]:
                    before[7] = j
            for j in range(16):
                if WB[12800+3072*r+16*j+before[11]] == goal[9]:
                    before[4] = j
            for j in range(16):
                if WB[13568+3072*r+16*j+before[ 8]] == goal[10]:
                    before[5] = j
            for j in range(16):
                if WB[14336+3072*r+16*j+before[ 9]] == goal[11]:
                    before[6] = j

            for j in range(16):
                if WB[11776+3072*r+16*WB[11520+3072*r+16*before[0]+before[10]]+j] == goal[0]:
                    before[13] = j
            for j in range(16):
                if WB[12544+3072*r+16*WB[12288+3072*r+16*before[1]+before[11]]+j] == goal[1]:
                    before[14] = j
            for j in range(16):
                if WB[13312+3072*r+16*WB[13056+3072*r+16*before[2]+before[8]]+j] == goal[2]:
                    before[15] = j
            for j in range(16):
                if WB[14080+3072*r+16*WB[13824+3072*r+16*before[3]+before[9]]+j] == goal[3]:
                    before[12] = j

            prebefore = [-1]*16

            #On reverse ensuite la 1ere partie de la boucle for

            for i in range(len(before)):
                for j in range(16):
                    if WB[256*r+16*i+j] == before[i]:
                        prebefore[i] = j

            goal = prebefore

        return prebefore


if __name__ == "__main__":
    #On commence par trouver le tableau entier de ECSC2019
    goal = [ int(i, 16) for i in binascii.hexlify("ECSC2019".encode()) ]
    solution = "".join([ hex(i)[2:] for i in solve(goal) ])
    {% raw %}
    print("ECSC{%s}" % solution)
```    {% endraw %}


## Le flag

En exécutant ce code on obtient l'output suivant : ECSC{976b459068a6ae6c}



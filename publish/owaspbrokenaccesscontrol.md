https://tryhackme.com/r/room/owaspbrokenaccesscontrol
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.40.180
```
![[Pasted image 20240501175827.png]]
## Reconocimiento de servicios
```
nmap -sVC -p22,80,443,3306 -Pn 10.10.40.180
```
![[Pasted image 20240501175950.png]]
## Gobuster
```
gobuster dir -u http://10.10.40.180 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x .php
```
![[Pasted image 20240501181016.png]]
# Fase de ataque
## Enumeración
1. Creamos una cuenta
2. Vemos como se llama la cuenta del admin
![[Pasted image 20240501180452.png]]
3. Entramos a /admin.php y nos damos permisos
![[Pasted image 20240501181113.png]]

Finish!
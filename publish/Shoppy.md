https://app.hackthebox.com/machines/Shoppy
# Fase de reconocimiento

## Buscamos puertos abiertos

```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.11.180
```
![[Pasted image 20241209091623.png]]
## Reconocimiento de servicios
```
nmap -sCV -p22,80,9093 10.10.11.180
```
![[Pasted image 20241209092230.png]]

## Gobuster
```
gobuster dir -u http://shoppy.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
```
![[Pasted image 20241209100558.png]]
Buscar subdominios
```
gobuster vhost -u http://shoppy.htb -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -t 200
```
mattermost.shoppy.htb
# Fase de ataque
## NoSQL injection
Se puede intentar realizar una consulta NOSQL
- Para sacar información se puede hacer un error
![[Pasted image 20241209105336.png]]
- Bypass the login diciendo que la pass no es (No funciona en este caso)
![[Pasted image 20241209105556.png]]
- MongoDB payload
```
' || '1'=='1
```
![[Pasted image 20241209105830.png]]
Login!

## NoSQL injection search
![[Pasted image 20241209111023.png]]
Encontramos passwords hasheadas en md5
![[Pasted image 20241209111100.png]]
Crackeamos las passwords
![[Pasted image 20241209111348.png]]
## Login en subdominio
Reutilizamos la password de josh

Information leakage
![[Pasted image 20241209113245.png]]

Iniciamos sesion por ssh con las credenciales encontradas
[-] ==pwned!==
# Privilege Escalation
```
sudo -l
```
![[Pasted image 20241209113707.png]]
De password-manager miramos las strings
```
strings -e l password-manager
```
![[Pasted image 20241209114242.png]]
También se puede utilizar ghydra

Ejecutamos como deploy y usamos la contraseña encontrada 
![[Pasted image 20241209114348.png]]

Nos conectamos como deploy
Este usuario esta en el grupo docker
![[Pasted image 20241209115420.png]]

Creamos un contenedor docker donde montamos toda la raiz en /mnt del contenedor

Transformamos los privilegios del bash en contenedor para convertirlo en SUID
![[Pasted image 20241209120333.png]]
![[Pasted image 20241209120422.png]]
[+] ==pwned!==
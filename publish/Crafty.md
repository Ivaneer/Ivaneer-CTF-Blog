https://app.hackthebox.com/machines/Crafty
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.11.249
```
![[Pasted image 20240511183604.png]]
La pagina web necesiat el nombre de dominio, añadimos en /etc/hosts
La pagina web nombra un puerto 1277 y minecraft
## Reconocimiento de servicios
```
nmap -sCV -p80,1277,25565 crafty.htb
```
![[Pasted image 20240511185718.png]]
# Fase de ataque
Este Java de Minecraft es vulnerable a Log4j
https://github.com/kozmer/log4j-shell-poc

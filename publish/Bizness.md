https://app.hackthebox.com/machines/Bizness
# Fase de reconocimiento
## Buscamos puertos abiertos
```
nmap -sS -p- -T5 --min-rate=5000 -vvv -Pn -n 10.10.11.252
```
![[Pasted image 20240511192902.png]]
## Reconocimiento de servicios
```
nmap -sCV -p22,80,443 10.10.11.252
```
![[Pasted image 20240511193155.png]]
```
ffuf -u https://bizness.thm/ -H "Host:FUZZ.creative.thm" -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt  -fw 6
```
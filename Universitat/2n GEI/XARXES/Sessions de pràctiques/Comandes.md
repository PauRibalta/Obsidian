
Per entrar:
**ssh prg17@187.33.151.15**
Contrasenya: prg17

# Sessió 1
## Repte 0

whoami
hostname
ip a 
ifconfig

Estàs al host o contenidor? Estem en un docker.

- IP: inet 10.10.0.43
- Màscara: netmask 255.255.255.0
- Xarxa: 10.10.0.0

Quants dispositius caben? 254 dispositius, la 0 i la 255 estan bloquejades.

## Repte 1

ip link

- És real o inventada? És inventada per temes de seguretat
- Fabricant?  1a:33:4c:
- Diferència IP vs MAC? 
	- MAC és de la capa 2
	- IP és de la capa 3

## Repte 2

ping 10.10.0.X
- Què passa si no existeix? Retorna error
- TTL? Time to live
- time? Temps que triga a arribar a mi
- TTL sempre igual? No
- time depèn de què? Distància, cues, routers

---

ping 8.8.8.8

 - Funciona? Sí
- Qui és? Google


## Repte 3

tcpdump -i eth0 icmp

Veus qui et pingueja a tu

## Repte 4

ip link set eth0 down

- Què passa amb el ping? No funciona perquè no deixes que te'l facin

ip link set eth0 up

- Diferència? Al tornar-lo a posar a up permets que et pinguejin de nou


## Repte 5

arp -a
- Mira qui t'ha fet ping
ip neigh flush all
- Fa flush de la llista
arp -a

- Diferència? Al fer la comanda per primera vegada surten els que t'han fet ping, i quan fas flush buides la llista.

- Com es torna a omplir? Si et tornen a fer ping tornaran a sortir a la llista quan facis arp -a

## Repte 6

ip addr add 10.10.0.200/24 dev eth0
- Afegeix una altre ip, el 24 del final són els bits de la xarxa

- Múltiples IPs? Sí, però com a secundàries, per a fer de man in the middle a transmissions externes

## Repte 7

ping 8.8.8.8

- Per què falla?

ip route 
ip route add default via 10.10.0.1

- Torna a fer ping

ping 8.8.8.8

- Funcionaria abans?

- Què és una ruta per defecte (default)?

apt update


hping3 --help

- Diferència amb ping? Te més opcions, configuracions. hping3 és més avançat que ping


## Resum comandes 20-4-2026

- ssh user@ip → connectar-te a un host remot
- whoami → veure usuari actual
- hostname → nom de la màquina
- ip a / ifconfig → veure IPs i interfícies
- ip link → info de la interfície i MAC
- ping ip → comprovar connectivitat i latència
- tcpdump -i eth0 icmp → veure trànsit ICMP (ping) en temps real
- ip link set eth0 down/up → desactivar/activar xarxa
- arp -a → veure taula ARP (IP ⇔ MAC)
- ip neigh flush all → buidar ARP
- ip addr add IP/mask dev eth0 → afegir IP a una interfície
- ip route → veure rutes
- ip route add default via IP → definir porta d’enllaç (internet)
- apt update → actualitzar repositoris
- hping3 → enviar paquets personalitzats (més avançat que ping)

# Sessió 2

### 1. Comandes de Configuració i Informació de Xarxa

- **ip route**: Serveix per veure i gestionar la taula d'encaminament (routing) del sistema. S'utilitza per saber quina és la ruta per defecte (_default gateway_) i si l'equip pot sortir a altres xarxes o a Internet.
    
- **ip route add default via IP*: Permet configurar la porta d'enllaç per defecte. Sense aquesta configuració, l'equip només es pot comunicar amb la seva xarxa local.
    
- **netstat -tan**: Mostra l'estat de les connexions TCP. S'utilitza per veure quines connexions estan establertes (`ESTABLISHED`), quines estan esperant a tancar-se (`TIME_WAIT`) i els ports locals i remots implicats.
    

### 2. Comandes de Transferència de Fitxers i Web

- **`curl`**: S'utilitza per fer peticions a servidors web des de la terminal. Per exemple, `curl -I` serveix per veure només les capçaleres de resposta del servidor (útil per verificar si hi ha connexió).
    
- **`wget`**: Una eina per descarregar fitxers o pàgines web senceres. Amb paràmetres addicionals (com es suggereix al repte 2), permet fer descàrregues recursives per baixar també les imatges i recursos d'una web.
    

### 3. Comandes de Diagnòstic i Traçabilitat

- **`ping`**: Serveix per verificar si un host remot és accessible i per mesurar la latència (temps de resposta). També permet veure el TTL (_Time To Live_), que dóna una pista de quants salts hi ha fins al destí.
    
- **`traceroute`**: Mostra tot el camí que segueix un paquet des del teu equip fins al destí, detallant cada node o encaminador (salt) pel qual passa. És fonamental per detectar on es talla la comunicació en una xarxa complexa.
    

### 4. Comandes d'Exploració i Seguretat (Nmap)

- **`nmap -sn [xarxa]`**: Realitza un _host discovery_ (escaneig de ping) per saber quins equips estan encesos en una xarxa determinada sense analitzar-ne els ports.
    
- **`nmap [IP]`**: Escaneja els ports més comuns d'un host per veure quins estan oberts (com el port 80 per HTTP o el 22 per SSH).
    
- **`nmap -p- [IP]`**: Escaneja els 65.535 ports existents d'un host (l'escaneig complet), ja que l'escaneig bàsic només en mira uns quants centenars.
    
- **`nmap -sV [IP]`**: Intenta detectar la versió del programari que corre en cada port obert (per exemple, saber quina versió exacta d'Apache o d'un servidor FTP s'està fent servir).
    
- **`nmap -O [IP]`**: Intenta detectar quin Sistema Operatiu (SO) està utilitzant el host objectiu basant-se en les respostes de la pila TCP/IP.
    

### 5. Gestió de Paquets (Utilitat del sistema)

- **`apt update`** i **`apt install`**: S'utilitzen per actualitzar la llista de repositoris i instal·lar les eines necessàries (com `wget`, `traceroute` o `nmap`) en sistemes basats en Debian/Ubuntu.
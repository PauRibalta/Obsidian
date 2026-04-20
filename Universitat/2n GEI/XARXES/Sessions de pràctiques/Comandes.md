
Per entrar:
**ssh prg17@187.33.151.15**
Contrasenya: prg17
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
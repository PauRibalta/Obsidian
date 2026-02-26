## Arquitectura de xarxa
### 1. Capes i protocols
El disseny arquitectural d’una xarxa es fa seguint un model de capes. Cada capa engloba un conjunt de tasques específiques, independents de les altres capes. Un model de capes suposa dos avantatges: 
1. Descomposa el problema en altres més tractables 
2. El disseny és més modular
	![[Pasted image 20260226200818.png]]
	
L’estructura de capes comporta dos fets: 
- La capa superior empra els serveis de comunicacions que li ofereix la capa inferior 
- Els missatges intercanviats entre dues capes homònimes obeeixen unes regles: protocols 

Tota capa (excepte la de maquinari) es comunica amb les capes homònimes mitjançant la capa inferior

En implementar una capa cal especificar:
- La interfície de comunicació entre capes adjacents
- El protocol entre capes homònimes
	- ![[Pasted image 20260226200959.png]]

Cada protocol té una estructura o format de paquets pròpia (PDU, Protocol Data Unit).
De forma general, un paquet consta de:
- Un camp d’informació (payload): el que volem transmetre (provinent de la capa superior)
- Camps de capçalera/cua (header/tail). Ubicats davant i/o darrere de la informació
	- ![[Pasted image 20260226201128.png]]
	  
Qualsevol paquet de nivell N, s’inclourà en un paquet de dades de nivell N-1 (Encapsulació), i així fins al nivell 1.
### 2. L’arquitectura OSI

Defineix 7 capes amb les seves funcionalitats:
1. **Físic**: Transmet els bits sobre el medi de comunicació(sincronització, modulació. . . ).
2. **Enllaç de dades**: Enllaç fiable node a node (adreçament local, protecció d’errors, accés al medi...).
3. **Xarxa**: Lliurament del paquet origen-destí (adreçament i encaminament).
4. **Transport**: Lliurament fiable de les dades a destri, és el primer nivell entre hosts
5. **Sessió**: Establir, mantenir i sincronitzar els sistemes (sincronització i control de di`aleg)
6. **Presentació**: Unifica la representació de la informació, no comú als hosts (enters 32 o 64 bits, estructures big-endian, little-endian. . . )
7. **Aplicació**: Protocols d’aplicació d’usuari.
   
- ![[Pasted image 20260226201239.png]]

### 3. L’arquitectura TCP/IP
- Empra un model m'és senzill de 4 nivells:
  
1. Hardware: Depèn del medi físic de transmissió i del tipus de xarxa (FDDI, Ethernet. . . )
2. Xarxa: El protocol predominant és IP (Internet Protocol). N’hi ha altres amb funcions addicionals (ARP, ICMP. . . ) Té funcions d’adreçament i encaminament
3. Transport: Dos protocols predominants: UDP (User Datagram Protocol) i TCP (Transmission Control Protocol). Té funcions de lliurament d’informació fiable al destí. Torna a ser el primer nivell entre origen i destí
4. Aplicació: Protocols d’aplicació d’usuari (FTP, HTTP, SMTP. . . )
   
- ![[Pasted image 20260226201505.png]]

### 4. L’arquitectura Client/Servidor
- Patró d’interacció entre aplicacions cooperatives
  
- **Servidor**
	- Qualsevol programa que ofereix un servei que pot ser accedit des de la xarxa per altres programes
- **Client**
	- Qualsevol programa que tramet una demanda al servidor i espera la seva resposta
	  
- ![[Pasted image 20260226201751.png]]
- ![[Pasted image 20260226201804.png]]

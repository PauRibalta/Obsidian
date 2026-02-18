## Arquitectura de xarxa
### 1. Capes i protocols
El disseny arquitectural d’una xarxa es fa seguint un model de capes. Cada capa engloba un conjunt de tasques espec´ıfiques, independents de les altres capes. Un model de capes suposa dos avantatges: 
1. Descomposa el problema en altres m´es tractables 
2. El disseny ´es m´es modular

L’estructura de capes comporta dos fets: 
- La capa superior empra els serveis de comunicacions que li ofereix la capa inferior 
- Els missatges intercanviats entre dues capes homònimes obeeixen unes regles: protocols 

Tota capa (excepte la de maquinari) es comunica amb les capes homònimes mitjançant la capa inferior

### 2. L’arquitectura OSI

Defineix 7 capes amb les seves funcionalitats:
1. **Físic**: Transmet els bits sobre el medi de comunicació(sincronització, modulació. . . ).
2. **Enllaç de dades**: Enllaç fiable node a node (adreçament local, protecció d’errors, accés al medi...).
3. **Xarxa**: Lliurament del paquet origen-destí (adreçament i encaminament).
4. **Transport**: Lliurament fiable de les dades a destri, és el primer nivell entre hosts
5. **Sessió**: Establir, mantenir i sincronitzar els sistemes (sincronització i control de di`aleg)
6. **Presentació**: Unifica la representació de la informació, no comú als hosts (enters 32 o 64 bits, estructures big-endian, little-endian. . . )
7. **Aplicació**: Protocols d’aplicació d’usuari.

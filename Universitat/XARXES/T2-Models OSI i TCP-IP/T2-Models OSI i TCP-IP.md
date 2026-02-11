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


### Què és?
 Memòria de capacitat reduïda i ràpida, situada en el nivell immediatament inferior al processador. Serveix per **guardar dades i instruccions que s’usen sovint**, així el processador no ha d’anar sempre a buscar-les a la memòria principal (RAM), que és més lenta.

**Paraula** = És la quantitat de bits que la CPU pot tractar “de cop”.

- Cache hit (encert). La posició referenciada pel processador és a la MC. 
- Cache miss (fallada). La posició referenciada no és a la MC. Cal iniciar un cicle d’accés a la memòria principal

Format de l'adreça de MP =[número bloc | paraula del bloc]

**Tag** = part de l’adreça de memòria que serveix per identificar **quina línia de memòria principal està guardada a la cache**.

### Configuracions 
- **Unificada/separada en instruccions i dades:**
	- Icache: emmagatzema les instruccions.
	- Dcache: emmagatzema les dades.
	- Cache unificada: emmagatzema tant instruccions com dades.
- **Interna/externa al processador:**
	- Interna: inclosa dins del mateix xip del processador.
	- Externa: Fora del xip del processador. Provoca més activitat en el bus del sistema.
- **Nombre de nivells que formen la memòria cache:**
	- Nivell únic.
	- Multinivell.

### Estructura MC/MP

La MP està formada per M blocs de $2^w$ paraules. Dins el bloc hi ha paraules d'una llargada concreta. Un bloc és la mínima quantitat d’informació que es transfereix entre MC i MP.

Bloc: mínima quantitat d’informació que es transfereix entre MC i MP.
Cache directory (CD): Conté adreça associada al bloc (Tag) i informació de determinades situacions del bloc.

![[Pasted image 20260225192418.png]]

### Operació de lectura

Aquest esquema mostra **què passa quan la CPU vol llegir una dada de la memòria cache**.
![[Pasted image 20260205174853.png]]
#### 1. La CPU demana una dada
La CPU envia una **adreça**, que es divideix en:

- **Direcció del bloc** → quin bloc és  
- **Paraula** → quina paraula dins del bloc  
#### 2. Es busca a la cache
Es mira al **directori de la cache (CD)** si aquest bloc hi és.

#### 3. Es comprova si hi és

#### Si hi és (hit)
1. S’obté el bloc de la cache  
2. Se selecciona la paraula  
3. S’envia al processador  
4. S’actualitza l’estat (LRU, etc.)

#### Si no hi és (miss)
1. Es va a la memòria principal (RAM)  
2. Es porta el bloc sencer  
3. S’ha de decidir on posar-lo → **algorisme de substitució**  
4. Es guarda a la cache  
5. Es selecciona la paraula  
6. S’envia a la CPU  

### Algorismes de correspondència
**Per què serveixen?** Indiquen a quins slots de MC pot ser copiat un bloc determinat de MP.

Dubtes : MP % MC = posició --> en quin algorisme s'aplica?

- **Directa :** es fa el mòdul entre la memòria principal i la cache MP % MC
	- Exemple: si la cache té 8 blocs:
		- 0 → bloc 0
		- 8 → bloc 0
		- 16 → bloc 0
	- Avantatges: molt ràpida, maquinari senzill i baix cost.
	- Inconvenients: molts conflictes (conflict misses) i si dos blocs volen la mateixa línia, es van substituint constantment.
	
- **Associativa :** un bloc de memòria principal **pot anar a qualsevol línia de la cache**.
	- Exemple:   si la cache té 8 blocs el número d'entrada pot anar a qualsevol.
	- Avantatges: molts pocs conflictes i millor taxa d’encerts (hit rate).
	- Inconvenients: maquinari molt més complex, comparadors per totes les línies i més costós.
- **Correspondència associativa per conjunts :** la cache es divideix en conjunts cada entrada només pot anar a un conjunt concret, però dins del conjunt pot ocupar qualsevol bloc.
	- Exemple : 8 entrades, 4 conjunts amb 2 blocs per conjunt:
		- Primer calculem:  conjunt = entrada % nombre conjunts
		- Després, dins del conjunt, pot ocupar qualsevol dels 2 blocs.
		
		-    | Conjunt | Blocs |
			|----------|-------|
			| 0            | 0, 1   |
			| 1            | 2, 3   |
			| 2            | 4, 5   |
			| 3            | 6, 7   |
		
	- Avantatges: menys conflictes que la directa, menys cost que la completament associativa i és la més utilitzada en CPUs modernes
	- Inconvenients: una mica més complexa que la directa


![[Pasted image 20260225194215.png]]

$$
\text{Taxa d'encerts (\%)} = \frac{\text{Nombre d'encerts}}{\text{Nombre total d'intents}} \times 100
$$

file:///C:/Users/pauri/Downloads/guia_memoria_cache%20(1).html

### Algorismes de substitució

- **LRU (Least Recently Used):** Dins del conjunt reemplaça el bloc que fa més temps que no s’ha referenciat.
- **FIFO (First In First Out):** Dins del conjunt reemplaça el bloc que porta més temps dins de la MC.
- **LFU (Least Frequently Used):** Reemplaça el bloc que ha tingut un menor nombre de referències.


### Política d'escriptura

- **Actualització immediata (Write-through)**
	- La MP s’actualitza al mateix temps que la MC. Si és un write miss s’actualitza només la MP.
	- Manté la coherència de dades entre MP i MC però genera molt tràfic de dades entre MP i processador
- **Actualització immediata mitjançant buffers (Buffered Write-through)**
	- Aïlla les operacions d’escriptura de la CPU de les escriptures a MP.
	- Redueix el temps d’espera del processador en operacions d’escriptura successives.
- **Actualització a posterioritat (Write-back)**
	- S’actualitza el bloc de MC i es marca com modificat. La còpia corresponent de MP s’actualitzarà quan calgui reemplaçar el bloc de la MC.
	- Redueix el tràfic de dades entre el processador i la MP
	- Planteja possibles situacions de incoherència de dades.

### Coherència de dades

![[Pasted image 20260225194531.png]]

Problema d’inconsistència de dades entre MC i MP si s’utilitza una política d’escriptura write-back sense “dirty” bits.

#### Solucions software: 
- El compilador ha de marcar els mòduls de codi i estructures de dades com compartit/noncacheable, exclusiu, etc.


-  **Opcions:**
	- Les pàgines compartides no es copien a la MC.
	- Copiar les pàgines compartides a la MC només per operacions de lectura.
	- Disposar de períodes d’ús exclusiu per a la MC per poder escriure en les pàgines compartides.
	- Incloure un directori que conté informació de tots els blocs de memòria i de l’estat en que es troben (opció adient per a sistemes multiprocessador).
#### Solucions hardware: 
- Transparents al S.O.
	- **Memòria exclosa de MC:** Les àrees compartides de MP es designen com excloses de MC. Els accessos a una zona exclosa seran fallades a la MC.
		- ![[Pasted image 20260225195100.png]]
	
	- **Memòria cache compartida:** Tots els màsters del bus són dirigits a MP a través de la mateixa MC.
		- Provocarà interferències entre els distints màsters del bus i el processador.
		- ![[Pasted image 20260225195249.png]]
		
	- **Bus snooping:** tots els màsters del bus comparteixen accés al mateix sistema de memòria i cada processador té la seva MC local.
		- El controlador de la MC monitoritza el bus per saber a quines adreces s’està accedint i endegar, si és necessari, les accions oportunes per mantenir les dades actualitzades.
		  
		- **Escenari 1:** El processador escriu a la seva MC local amb write-back. La MP queda amb dades antigues. 
		  Solució: El controlador de la MC monitoritza el bus per a les lectures. Si l’adreça està copiada a la MC serà ella mateixa qui faciliti les dades per lectura.
		
		- Escenari 2: El processador escriu a la seva MC local amb write-through i per tant la MP s’actualitza al mateix temps. Si un dispositiu escriu a la MP, la MC queda amb dades antigues. 
		  Solució: El controlador de MC monitoritza el bus per a les escriptures.
		
		- ![[Pasted image 20260225195506.png]]
		
	- **Protocol MESI:** cada bloc de la MC incorpora bits d’estat indicant que fan els màsters del bus respecte la memòria. Hi ha quatre estats MESI que defineixen si un bloc és vàlid, si està disponible en altres MC del sistema i si ha estat modificat.
	  
		- **Modified (M):** el bloc està copiat només a la pròpia MC i ha estat modificat (la MP té dades no vàlides). Aquest bloc pot ser actualitzat localment a la MC sense accedir al bus. Abans de passar a l’estat M, totes les les còpies del bloc a les altres MC i a la MP han de passar a l’estat I.
		  
		- **Exclusive (E):** indica que el bloc està copiat només a la pròpia MC i que no ha estat modificat (la MP té una còpia vàlida). L’actualització del bloc causa la transició a l’estat Modificat.
		  
		- **Shared (S):** indica que el bloc està compartit per altres MC i no ha estat modificat. Pot ser llegit per la CPU sense accedir a MP. Una operació d’escriptura causarà un cicle d’escriptura a MP a través del bus i una invalidació de la còpia del mateix bloc a les altres MC.
		  
		- **Invalid (I):** indica que el bloc no està disponible a la MC o no és vàlid.
		  
		- **Transaccions amb el processador:**
			- PrRd (Proc. Read). Lectura del processador a la MC.
			- PrWr (Proc. Write). Escriptura del processador a la MC.
			  
		- **Transaccions amb el bus:**
			- BusRd (Bus Read). Lectura del controlador de MC mitjançant el bus. El sistema de memòria o una altra MC facilitarà les dades a través del bus.
			- BusRdX (Bus Read Exclusive). Sol·licitud d’una còpia exclusiva d’un bloc per modificar-la. Les altres MC invaliden la seva còpia.
			- BusWB (Bus Write Back). Post-escriptura a MP.
			  
		- **Transaccions tipus BusRd amb comprovació del senyal de bloc compartit S:**
			- BusRd (¬S). No s’ha confirmat el senyal S al fer BusRd.
			- BusRd(S). Està confirmat el senyal S al fer BusRd.
			- Quan es dona la transacció BusRd simple, significa que el valor del senyal S per aquesta transició és indiferent
			  
			  
		![[Pasted image 20260225181359.png]]

#### Rendiment: 
- ts: temps mig d’accés a memòria. 
- h: índex d’encerts. 
- tc: temps d’accés a la MC. 
- tm: temps d’accés a la MP.
  
- h = n. encerts / n. referències a memòria
- índex fallades = 1 - h
  
- ts = h·tc + (1-h)·tm (Arquitectura look-aside)
- ts = tc + (1-h)·tm (Arquitectura look-through)
  
- **Influència de la capacitat de la MC:**
	- Prou petita perquè l’augment del cost global del sistema no sigui significatiu.
	- Prou gran perquè ts s’aproximi el més possible a tc
- **Influència de la mida del bloc de MC:**
	- Blocs petits:
		- Menor temps de transferència entre MC i MP.
		- Menys susceptibles de contenir informació innecessària.
	- Blocs grans:
		- Major captura de la localitat espacial
		- Menys circuiteria pel maneig dels identificadors d’adreça i bits d’estat.
		  
	  ![[Pasted image 20260225200830.png]]
	- ![[Pasted image 20260225200917.png]]
	- ![[Pasted image 20260225200935.png]]


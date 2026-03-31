## Control d'accés al medi

### CSMA
- CSMA: Accés múltiple per detecció de portadora.
- Abans d’efectuar una transmissió, comproven l’estat del medi.
- En funció de l’estat obtingut prenem decisions sobre quan efectuar la transmissió.
- Hi ha diferents versions de CSMA:
	- CSMA no persistent:
		- Medi lliure → transmet immediatament la trama.
		- Medi ocupat → espera un temps aleatori per tornar a intentar transmetre la trama.
		- Sistema poc agressiu.
		- ![[Pasted image 20260312122336.png]]
		  
	- CSMA persistent
		- Més agressiu que el no persistent. Procura enviar les dades el mes aviat possible.
		- Diferenciarem dos tipus de CSMA persistent:
			- CSMA 1-persistent
				- Medi lliure → transmet immediatament la trama.
				- Medi ocupat → es manté “escoltant” el medi fins que detecta que està lliure.
				- ![[Pasted image 20260312122609.png]]
				  
			- CSMA p-persistent
				- S’aplica a canals ranurats.
				- Medi lliure → transmet amb una probabilitat p. Amb una probabilitat q = 1 − p espera fins la següent ranura, aplicant de nou la probabilitat.
				- Medi ocupat i no és el primer intent de transmetre  → espera un temps aleatori.
				- ![[Pasted image 20260312122751.png]]

### Detecció de col·lisions

#### Col·lisió
- Coincidència de dos senyals de dades en el medi de transmissió
- Son degudes als temps de propagació dels senyals en els medis de transmissió.
- Si un node ocupa el medi, la seva transmissió trigarà un temps en arribar a un node distant.
- Durant aquest temps, el node distant, detecta el medi lliure i pot efectuar una transmissió
	- ![[Pasted image 20260313122423.png]]
	- ![[Pasted image 20260313122432.png]]
	- ![[Pasted image 20260313122443.png]]
	- ![[Pasted image 20260313122455.png]]
	- ![[Pasted image 20260313122504.png]]
	- ![[Pasted image 20260313122513.png]]

### Detecció de col·lisions
- La detecció de col·lisions és un procés analògic que es duu a terme en l’adaptador de xarxa
- Al mateix temps que transmet es comprova la recepció. Si rep un senyal diferent del que esta emetent es tracta d’una col·lisió
- En detectar una col·lisió el node deixa immediatament de transmetre, evitant l’ocupació innecessària del medi
- Per la detecció de col·lisions s’han de tenir en compte dos paràmetres:
	- El temps de propagació del senyal
	- El temps de transmissió
![[Pasted image 20260313122850.png]]
![[Pasted image 20260313122900.png]]
![[Pasted image 20260313122911.png]]
![[Pasted image 20260313122921.png]]
![[Pasted image 20260313122931.png]]
![[Pasted image 20260313122946.png]]
![[Pasted image 20260313123001.png]]

#### Exercici
![[Pasted image 20260313123048.png]]

S'arrodoneix a l'alça SEMPRE, és a dir que no pots tenir 0,2 octets sempre hauràs d'agafar-ne un sencer.
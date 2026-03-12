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
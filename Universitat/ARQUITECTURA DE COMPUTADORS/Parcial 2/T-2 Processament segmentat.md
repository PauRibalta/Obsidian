### Conceptes bàsics

Execució d’una instrucció: 
	• Període de captació (F: Fetch). 
	• Període d’execució (E: Execute).
Objectiu de la segmentació: solapar els distints períodes per tal de fer-ne una execució paral·lela.

 **Segmentació de 4 etapes:** 
	• Captació (F: Fetch). Llegir la instrucció de memòria.
	• Decodificació (D). Decodificació de la instrucció i lectura dels operands font. • 
	• Execució (E). Realització de l’operació especificada a la instrucció. 
	• Actualització (W: Write). Escriptura del resultat a l’adreça destí.
	![[Pasted image 20260409173135.png]]

• Cada etapa s’ha de completar en un cicle de rellotge. 
• El temps de cicle ha de ser el de l’etapa més llarga. 
• Objectiu: disposar d’etapes amb durada similar.
• Temps mig per instrucció de la màquina segmentada
	t instr = t. instr màquina no segmentada / nº etapes
	
• En condicions ideals, la millora en velocitat és igual al nombre d’etapes. 
• La segmentació augmenta la productivitat del processador però no redueix el temps d’execució individual de cada instrucció.

### Segmentació

**Requeriments de funcionament:**
	• Cal assegurar el no solapament de l’ús de recursos en les distintes etapes. 
	• El PC cal incrementar-lo en cada cicle de rellotge (etapa Fetch). Cal afegir un incrementador a part de la ALU. 
	• A cada cicle de rellotge cal llegir una nova instrucció (F) i es pot necessitar una nova paraula de dades. Això implica dos accessos a memòria per a cada instrucció (Possible solució: Icache + Dcache). 
	• Els accessos a memòria cal que siguin ràpids (memòria cache on-chip).

**Aturades del pipeline:**
	Una etapa dura més que les altres degut a que cal realitzar una operació puntual que tarda més temps.
	![[Pasted image 20260409174559.png]]
	Es produeix una fallada a la memòria cache.
	![[Pasted image 20260409174711.png]]

A l'hora de fer els càlculs de temps pels exercicis, t'has de quedar amb el temps de 'ordre que dura més, com el primer exemple de els aturades del pipeline.

**Speed-up** = segmentat (4 etapes sumades) / seqüencial (temps etapa més llarga)
**Eficiència del sistema** = speed-up / valor màxim teòric
**Valor màxim teòric** = nº d'etapes

### Riscos

Situacions que es produeixen en l’execució segmentada que impedeixen que s’executi la següent instrucció del flux d’instruccions en el seu cicle de rellotge designat. Implicarà una reducció de la velocitat.

**Tipus de riscos:**
	• Estructurals: conflictes en l’ús de recursos.
		• Alguna unitat funcional no està completament segmentada. No es pot iniciar una seqüència d’instruccions que utilitzen la mateixa unitat
		• Hi ha recursos que no s’han duplicat suficientment (ex. un únic port de memòria per a instruccions i per a dades).
	• De dades: els operands no estan disponibles a l’etapa que es necessiten.
		Es presenten quan la segmentació canvia l’ordre d’accés als operands en relació a l’ordre normal en que apareixen les instruccions que s’han d’executar seqüencialment.
		![[Pasted image 20260409180702.png]]
		**Avançament d’operands:** El resultat de la ALU es realimenta cap als seus buffers d’entrada. Si el h/w d’avançament detecta que l’operació prèvia a la ALU ha d’escriure a un registre que és operand de la instrucció actual, es selecciona el resultat avançat com entrada a la ALU.
		![[Pasted image 20260409181238.png]]
		**Classificació:** 
			Donades dues instruccions i,j, on i s’executa abans que j, es poden donar els següents riscos de dades: 
			• RAW (Read after Write). La instrucció **j vol llegir un valor que encara no s’ha acabat d’escriure per i** 
			• WAR (Write after Read). La instrucció **j escriu en un registre que i encara ha de llegir** 
			• WAW (Write after Write). La instrucció j escriu un operand abans que sigui escrit per i.
		
	
• De control (o d’instruccions): Provocats per les instruccions de salt i altres que modifiquen el registre PC
	- Salts incondicionals: Causen una aturada del pipeline (penalització de salt)
			- ![[Pasted image 20260415174459.png]]
		- La penalització de salt és més gran quan el pipeline té més etapes. Aquesta penalització es pot reduïr quan l’adreça de salt es calcula a l’etapa de decodificació.
			- ![[Pasted image 20260415174545.png]]
			- Tècnica de prefetching: Utilització d’unitats específiques per a la captació d’instruccions abans que siguin necessàries i les situen en una cua.
				- ![[Pasted image 20260415174815.png]]
	- Salts condicionals: La decisió de saltar no es pot prendre fins que s’acaba l’execució de la instrucció
		- Salt retardat: reordenació de les instruccions per incloure en el forat de salt una instrucció útil.
			- ![[Pasted image 20260415174933.png]]
		- Predicció de salt: predir el salt com no efectiu. Es continuen captan instruccions per ordre. Es fa una execució especulativa de les instruccions fins que es pot comprovar que s’han estat executant les correctes.
			- ![[Pasted image 20260415175430.png]]
		- Predicció dinàmica:
			- ![[Pasted image 20260415175542.png]]
			- Si ens equivoquem a l'hora de predir si saltarà o no perdem la instrucció.
	
	

**Implicacions en el rendiment:**
	$\text{Acceleració del pipeline} = \frac{t_{\text{mig instr. sense segmentació}}}{t_{\text{mig instr. amb segmentació}}} = \frac{\text{CPI ideal} \cdot \text{profunditat de segmentació}}{\text{CPI ideal} + \text{cicles de detenció}}$ 

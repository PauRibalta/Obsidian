# Medis de transmissió

1. **Conceptes teòrics**:
	- **Medi de transmissió:** Camí físic que permet el transport de l’energia del senyal.
	
	- **Rendiment d'un sistema de transmissió:** $R_{dB} = 10 \log_{10}\left(\frac{P_s}{P_e}\right)$, on ${P_s}$ és la potència de sortida del sistema i ${P_e}$ és la potència d'entrada del sistema.
	
	- **Teorema de Nyquist (1924)**: Un senyal d’ample de banda de W Hz. pot ser reconstruït prenent mostres a una velocitat de 2W mostres per segon.
	
	- Velocitat de transmissió: $V = 2W \cdot \log_2(N)$





![[Pasted image 20260224153515.png]] Entra a examen

### Transmissió de senyals
- El pas d’un senyal per un medi de transmissió provoca alteracions en el senyal. 


- Relació senyal/soroll (en dB): $(S/N)_{dB} = 10 \cdot \log_{10}\left(\frac{P_s}{P_n}\right)$
- El rendiment d’un sistema de transmissió es mesura en decibels ( dB): $R_{dB} = 10 \cdot \log_{10}\!\left(\frac{P_s}{P_e}\right)$

- El decibel també es pot emprar per a mesurar diferències de voltatge: $R_{dB} = 20 \cdot \log_{10}\!\left(\frac{V_s}{V_e}\right)$

- El teorema de Nyquist estableix la velocitat de transmissió necessària com: $v = 2 \cdot W \cdot \log_2 N \; \text{(bps)}$


- Teorema de Shannon: La capacitat màxima, en bits per segon, d’un canal d’ample de banda  W amb presència de soroll tèrmic ve donada per: $C = W \cdot \log_2\left(1 + \frac{S}{N}\right)\ \text{(bps)}$


## Tipus de medis

### Transmissió de la llum basada en la  llei d’Snell: 
$$  
n_1 \sin(\theta_1) = n_2 \sin(\theta_2)  
$$
$$  
n_1, n_2 \text{ índex de refracció}  
\quad \left( n = \frac{c}{v} \right)  
$$
$$  
\text{on } c \text{ és la velocitat de la llum en el buit i }  
v \text{ la velocitat en el medi}  
$$
$$  
\theta_c = \sin^{-1}\!\left(\frac{n_2}{n_1}\right)  
\quad \text{(angle crític)}  
$$
$$  
\text{Només té sentit quan } n_1 > n_2  
$$

### Medis aeris:

- **Longitud d'ona:** $\lambda = \frac{c}{f} \; \text{(c: velocitat de la llum al medi), on } f \text{ és la freqüència d'oscil·lació}$

- **Pèrdues de propagació:** $$
L_{dB} = 10 \cdot \log_{10} \left(\frac{4 \pi d}{\lambda}\right)^2 
       = 20 \cdot \log_{10} \left(\frac{4 \pi d}{\lambda}\right), 
\quad d \text{ és la distància de l'enllaç}
$$
### Antenes:

- **Guany proporcional:** $G_{dBi} \sim 10 \log_{10} \left[ 0.5 \left(\frac{\pi D}{\lambda}\right)^2 \right]$, ***D** és el diàmetre *

### Satel·lits: Repetidors orbitals

- Freqüències de pujada i baixada diferents per evitar interferències a bord i $f_{up} > f_{down}$

- **Rendiment:**  Utilitzant la fórmula del guany proporcional (entra a examen)$$
Rendiment = G_{t,up}|_{dB} - L_{up}|_{dB} + G_{s,up}|_{dB} + A|_{dB} 
+ G_{s,down}|_{dB} - L_{down}|_{dB} + G_{t,down}|_{dB}
$$
![[Pasted image 20260224161453.png]]

## Codificació de línia

### Velocitat de transmissió i velocitat de modulació

**Velocitat de transmissió:** $V_t = \frac{1b}{T_bs} \; \text{bps}, \; \text{on } T_b \text{ és el temps de bit}$

**Velocitat de modulació:** $V_m = \frac{V_t}{n} \; \text{bauds}, \; \text{on } n \text{ és el nombre de bits per element de senyal}$ 
- N = 4/5 en 4B5B
- N= 1 en NRZ i NRZI
- N = 0,5 en Manchester

## Senyals modulats

#### Fases 
- La modulació/demodulació de senyals es produeix en 3 fases: 
	- Agrupació de bits en símbols. P.e. modulació quaternària:
		- 00 → s00 
		- 01 → s01 
		- 10 → s10 
		- 11 → s11
		- en aquest cas, Vt (vel. transmissió) = 2 · Vm (velocitat de modulació)
	- A cada símbol se li atorga una amplada i fase determinada: constel·lació de símbols
	- Els símbols es transmeten a la freqüència portadora  adequada al medi $\omega_p = 2\pi f_p$

#### Constel·lacions
- **BPSK:** Binary Phase Shift Keying
- **QAM:** Quadrature Amplitude Modulation 
- ![[Pasted image 20260226152127.png]]

## Entramat
- En els medis de transmissió es produeixen alteracions del senyal → errors en la informació que transporten
- Per tant, cal controlar i gestionar l’intercanvi de les dades
- El control es duu a terme emprant un protocol entre emissor i receptor. El protocol implica afegir informació de control a les dades.
- Les dades s’estructuren en trames que, a banda de les pròpies  dades, inclouen capçaleres i camps de control.
- La informacio addicional comporta una disminució del rendiment, en bps, de les línies de transmissió.
	- Taxa bruta: Velocitat de transmissió (dades + info. control)
	- Taxa útil: Velocitat de transmissió de la informació o útil (dades)
	  
- #### Mètodes per generar la trama:
	- **Protocols orientats a caràcter**
		- **Indicadors d’inici i final (sentinella)**
			- Caràcters especials per indicar l’inici i el final de les dades. Si apareixen en les dades caràcters de control s’anteposa el caràcter DLE  data-link escape
				- **BISYNC** (Binary Synchonous Communication)
					- ![[Pasted image 20260226194305.png]]
			  
				- **PPP** (Point-to-Point Protocol)
					- ![[Pasted image 20260226194336.png]]
			  
		- **Comptadors de caràcters**
			- Inclouen dins de la trama un camp que indica la longitud, en caràcters, de les dades
				- **DDCMP** (Digital Data Communication Message Protocol)
					- ![[Pasted image 20260226194523.png]]
	- **Protocols orientats a bit**
		- Consideren les dades com una seqüencia de bits
		- Cada trama conte una seqüencia de bits d’inici i una de final
		- Si les dades contenen la seqüencia d’inici o final s’insereix un bit per diferenciar-les
			- **HDLC** (High-Level Data Link Control)
				- ![[Pasted image 20260226194732.png]]
				  
		- Control de les seqüencies d’inici i final:
			- Si en les dades apareixen 5 bits 1 consecutius, el transmissor insereix un 0 per diferenciar-ho de les seqüències d’inici i final.
			- El receptor, en rebre 5 bits 1 consecutius, mira el valor del següent bit:
				- 0 → Es tracta d’un bit de farciment i el menysprea
				- 1 → Cal comprovar el següent bit
					- 0 → Final de trama
					- 1 → Error en les dades, espera rebre el final de trama i la rebutja
					  
	- **Entramat síncron**
		- Basat en un únic rellotge d’alta precisió (rellotge mestre)
		- Hi ha un flux de trames constant encara que no hi hagi informació per transmetre
			- **SONET** (Synchronous Optical Network)
				- Un sistema SONET esta format per commutadors, multiplexors i repetidors
				- ![[Pasted image 20260226195536.png]]
				  
				- L’element basic de SONET és la trama STS-1 ( Synchronous Transport Signal-1).
				- Constituïda per 810 octets que es transmeten cada 125µs (VT = 51.84 Mbps).
				- La trama SONET es pot representar com una matriu de 9 files per 90 columnes (9 x 90 bytes).
				- Les 3 primeres columnes es reserven per informació d’administració del sistema.
					- Les 3 primeres files es reserven per informacio suplementària de les seccions
					- Les 6 files restants es destinen a informacio suplementària de línia
				- Les 87 columnes restants contenen les dades d’usuari SPE (Synchronous Payload Envelope)
				- Com la SPE pot començar en qualsevol lloc de la trama, s’empra la primera fila de la informacio suplementària de línia com a punter al primer byte de la SPE
				- Dins de la SPE, el primer byte conte informació suplementària de la ruta
					- ![[Pasted image 20260226195918.png]]
					  
				- Emprant TDM (Time-Division Multiplexing), SONET combina diverses STS-1 per formar una STS-n. Cada STS-n es genera amb els bytes de les n STS-1.
					- ![[Pasted image 20260226195952.png]]
					  
## Detecció d'errors

Forma part del control de l’enllaç¸ de dades i es imprescindible per a la comunicació entre nodes.
- ![[Pasted image 20260226200141.png]]
- ![[Pasted image 20260226200201.png]]
- La tècnica de detecció d’errors consisteix en afegir informació addicional en la trama que permeti al receptor determinar si s’han produït errors durant la transmissió
- Donat un missatge de n bits, la quantitat de bits a afegir per al control d’errors, k, ha de complir: k << n.
  
- #### Comprovació de paritat
	- Consisteix en afegir un bit de paritat al final d’un bloc de dades. El valor d’aquest bit es determina de forma que el bloc resultant tingui un nombre parell de 1 (paritat parell) o bé un nombre senar (paritat senar)
	  
	- **Paritat unidimensional**
		- Les dades es divideixen en blocs de n bits i a cada bloc s’afegeix el bit de paritat.
		- El receptor calcula la paritat de cada bloc i ho compara amb el bit de paritat rebut per determinar si s’han produït errors. Aquest mètode detecta els errors que afectin a un nombre senar de bits.
		- Un exemple típic era la transmissió ASCII: A cada caràcter ASCII de 7 bits se li afegia un de paritat
			- ![[Pasted image 20260226200440.png]]
			  
	- **Paritat bidimensional**
		- Es consideren les dades com una matriu de bits i es calcula la paritat tant de les files com de les columnes.
		  
			- ![[Pasted image 20260226200545.png]]
			  
			  
- #### Suma de comprovació
	- Empra l’aritmètica de complement a 1.
		- Es consideren les dades com una seqüencia d’enters de 16 bits.
		- Se sumen cadascun dels blocs de 16 bits.
		- Si hi ha carry, aquest se suma al resultat.
		- El complement a 1 del resultat s’empra com a suma de comprovació.
	- Gran facilitat d’implementació software.
	- Emprat com a deteccio d’errors en TCP/IP.
		- ![[Pasted image 20260312113906.png]]
		- ![[Pasted image 20260312113915.png]]
		  
- #### CRC
	- Es el tipus més potent i més emprat.
	- **Funcionament:**
		- El **transmissor** i el **receptor** acorden prèviament un número (polinomi) que s'utilitzarà per calcular el CRC.
		- Transmissor:
			- El transmissor, partint dels n bits de les dades, genera una seqüencia de k bits que afegeix a les dades.
			- El bloc resultant de n + k bits ha de ser divisible pel “número”  acordat.
		- Receptor:
			- El receptor divideix els n + k bits rebuts pel “número” acordat per obtenir el residu.
			- Si el residu és diferent de zero, determina que les dades rebudes contenen errors.
	- **Notació polinòmica:**
		- Per efectuar les operacions s’utilitza l’aritmètica mòdul 2.
		- La posició del bit indica l’exponent.
		- Només s’inclouen els termes amb **bit = 1**.
		- Cal escollir un polinomi divisor D(X) de grau r igual al nombre de bits del CRC.
	- **Errors detectats pel CRC:**
		- Suposem que a la informació enviada per el transmissor, B(X), s’afegeixen errors en la transmissió, E(X).
		- Únicament els errors E(X) que siguin múltiples de D(X) no seran detectats. Sí detectarem:
			- Errors d’un bit
			- Errors dobles si D(X) te al menys tres factors
			- Qualsevol nombre senar d’errors si D(X) te el factor (X+1)
			- Errors en ràfegues de longitud inferior o igual a r
			- Ràfegues grans d’errors:
				- Ràfegues de longitud $r+1$ amb una probabilitat de $1 - \frac{1}{2^{r-1}}$
				- Ràfegues de longitud superior a $r+1$ amb probabilitat $1 - \frac{1}{2^r}$
			- Polinomis divisors estandarditzats:
				- ![[Pasted image 20260312115324.png]]

## Protocols ARQ

- La detecció d’errors permet al receptor diferenciar les trames correctes de les trames errònies.
- Si el receptor descarta una trama, l’emissor haurà de tornar a enviar-la per evitar la pèrdua d’informació
- .Per tal què l’emissor tingui coneixement de les trames que ha acceptat el receptor i les que s’han rebutjat per errors, s’empren trames de reconeixement, ACK (acknowledgment), i temporitzadors.
- Aquests mecanismes s’anomenen sol·licitud de repetició automàtica, ARQ (Automatic Repeat reQuest)

### Parada i espera
- L’emissor envia una trama i espera el reconeixement de la trama per part del receptor. Alhora, inicialitza un temporitzador.
- Si expira el temporitzador abans de rebre l’ACK, torna a transmetre la mateixa trama i inicialitza de nou el temporitzador.
- Si es rep el reconeixement abans què expiri el temporitzador, transmet la següent trama i inicialitza el temporitzador.
	- ![[Pasted image 20260312115909.png]]
	  
- Els errors poden afectar la trama de reconeixement (o aquest pot arribar després de finalitzar el temporitzador).
- En aquest cas l’emissor tornaria a transmetre la trama.
- El receptor acceptaria la trama com una nova trama quan en realitat es una còpia de l’anterior.
	- ![[Pasted image 20260312120011.png]]
	  
- Per evitar aquesta situació s’empra un bit com a número de seqüència per etiquetar tant les trames con les confirmacions.
- Si es deteriora la trama d’ACK i l’emissor torna a enviar la trama, el receptor detectarà que és una còpia i la descartarà (però tornarà a enviar el reconeixement de la trama).
	- ![[Pasted image 20260312120138.png]]

### Finestra lliscant
- S'aprofita millor el canal
- Evita l’espera d’un ACK per poder enviar una altra trama
- Permet enviar un conjunt de trames sense tenir que esperar reconeixement de la trama n per enviar la trama n + 1
- Variables inicialitzades en el transmissor:
	- SWS (Sender Window Size): Indica la quantitat màxima de trames que es poden enviar amb reconeixement pendent
	- LAR (Last Acknowledgement Received): Número de seqüència de la trama de la que s’ha rebut l’últim ACK
	- LFS (Last Frame Sent): Numero de seqüència de la última trama enviada
- El transmissor ha de mantenir sempre que:
	- LFS − LAR ≤ SWS
- Per a cada trama emesa el transmissor desplaça a la dreta LFS i inicia un temporitzador.
- Si el temporitzador finalitza i no s’ha rebut reconeixement es torna a enviar la trama.
- Quan el transmissor rep un reconeixement desplaça a la dreta LAR el número de trames acceptades, permetent-li enviar noves trames.
	- ![[Pasted image 20260312120255.png]]
	  
- En el receptor s’assignen també tres variables:
	- RWS (Receiver Window Size): Quantitat màxima de trames fora de seqüència que es poden acceptar
	- LAF (Largest Acceptable Frame): Numero de seqüència màxim d’una trama per acceptar-la
	- LFA (Last Frame Acknowledged): Numero de seqüència del darrer ACK enviat
	- En rebre una trama, el receptor efectua una de les dues accions següents:
		- Si LFA < Num. Seq. ≤ LAF la trama es acceptada
			- ![[Pasted image 20260312120551.png]]
			  
		- Altrament, rebutja la trama per estar fora del marc de la finestra
		- ![[Pasted image 20260312120606.png]]
		  
### Número de seqüència
- El número de seqüència de trama forma part de la capçalera de les trames.
- La seva longitud es, per tant, limitada.
- En superar el seu valor màxim torna a començar des de zero.
- Aquesta situació pot presentar problemes en el receptor si no es tria una mida de la finestra adequada
- Per evitar aquesta situació s’ha d’escollir:
	- $RWS = SWS \le \frac{\text{(Num. Seq. Max} + 1)}{2}$
	- ![[Pasted image 20260312121100.png]]

### Rebuig de trames
- Les temporitzacions afecten a l’eficiència en tenir que esperar un temps per tornar a enviar una trama rebuda amb errors.
- Per millorar l’eficiència s’introdueixen trames de reconeixement negatiu NAK (Negative AcKnowledgment)
- NAK l’envia el receptor per indicar el rebuig d’una trama. El transmissor reenviarà la trama rebutjada i les trames següents.
	- ![[Pasted image 20260312121223.png]]


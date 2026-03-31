Els dos paràmetres més emprats per avaluar el rendiment d’una xarxa són el retard i el cabal.       

- Retard: Té 3 components:
	- *Propagació*: Depèn de la distància i la velocitat de la llum en el medi (Vi)
	- *Transmissió*: Depèn de la mida del paquet i de la velocitat de transmissió (Vt).
	- *Cues*: Depèn dels commutadors de la xarxa.
	
	Calcular Retard Propagació: $$
R_p = \frac{\text{dist}}{\text{V llum en el medi}}
$$
	Calcular Retard Transmissió: $$
R_t = \frac{\text{mida paquet}}{\text{V transmissió}} \; (\text{bit/s})
$$
	Calcular Retard Cues (no entra examen): $$
R_c = (\text{paquets en cua}) \cdot R_{t\text{ paquet en cua promig}}
$$
$$
Aprox = \frac{\text{Intensitat ús}}{\text{1-Intensitat ús}} \cdot\frac{\text{Long. mitjana}}{\text{V transmissió}}  \; (\text{bit/s})
$$
	Retard Total: $$
R = R_p + R_t + R_c
$$
	Calcular Intensitat d'ús: 
$$
L_n = \text{longitud mitjana de la cua}, \qquad
\alpha = \text{freqüència mitjana d’arribada}
$$
$$
I = \frac{a \cdot L_n}{V_t}
$$
- RTT = (Rp+Rt+Rc) * 2
- Cabal: Quantitat d’informació que es pot transferir per unitat de temps.
- El producte retard propagació · capacitat (Vt) dona una idea de la **quantitat de bits que hi caben a la xarxa.**

![[Pasted image 20260205164022.png]]



Els dos paràmetres més emprats per avaluar el rendiment d’una xarxa són el retard i el cabal.       

- Retard: Té 3 components:
	- *Propagació*: Depèn de la distància i la velocitat de la llum en el medi (Vi)
	- *Transmissió*: Depèn de la mida del paquet i de la velocitat de transmissió (Vt).
	- *Cues*: Depèn dels commutadors de la xarxa.
	
	Calcular Retard Propagació: $$
R_p = \frac{\text{dist}}{V_l}
$$
	Calcular Retard Transmissió: $$
R_t = \frac{\text{mida}}{V_t} \; (\text{bit/s})
$$
	Calcular Retard Cues: $$
R_c = (\text{paquets en cua}) \cdot R_{t\text{ paquet en cua promig}}
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
- Cabal: Quantitat d’informació que es pot transferir per unitat de temps.

![[Pasted image 20260205164022.png]]



Retard: Té 3 components:
	- *Propagació*: Depèn de la distància i la velocitat de la llum en el medi (Vi)
	- *Transmissió*: Depèn de la mida del paquet i de la velocitat de transmissió (Vt).
	- *Cues*: Depèn dels commutadors de la xarxa.

- Calcular Retard Propagació:$$
R_p = \frac{\text{dist}}{\text{V llum en el medi}}
$$
- Calcular Retard Transmissió:$$
R_t = \frac{\text{mida paquet}}{\text{V transmissió}} \; (\text{bit/s})
$$
- Calcular Retard Cues:$$
R_c = (\text{paquets en cua}) \cdot R_{t\text{ paquet en cua promig}}
$$
$$
Aprox = \frac{\text{Intensitat ús}}{\text{1-Intensitat ús}} \cdot\frac{\text{Long. mitjana}}{\text{V transmissió}}  \; (\text{bit/s})
$$
Retard Total:$$
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

### Al tema 3 és on més coses hi han
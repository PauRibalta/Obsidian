# Medis de transmissió

1. **Conceptes teòrics**:
	- **Medi de transmissió:** Camí físic que permet el transport de l’energia del senyal.
	
	- **Rendiment d'un sistema de transmissió:** $R_{dB} = 10 \log_{10}\left(\frac{P_s}{P_e}\right)$, on ${P_s}$ és la potència de sortida del sistema i ${P_e}$ és la potència d'entrada del sistema.
	
	- **Teorema de Nyquist (1924)**: Un senyal d’ample de banda de W Hz. pot ser reconstru¨ıt prenent mostres a una velocitat de 2W mostres per segon.
	
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

